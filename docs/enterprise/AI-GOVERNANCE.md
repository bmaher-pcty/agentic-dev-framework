# Enterprise AI Governance Guidance

> **Audience:** Platform engineering teams distributing the agentic-dev-framework across an organization.  
> **Scope:** Data governance, model compatibility, corporate proxy configuration, and framework ownership.  
> **Related:** `docs/FRAMEWORK-SPEC.md` (core contracts), `docs/adapters/` (model-specific adapters)

---

## Data Governance: What the Framework Sends to AI Models

### What Is Sent

When any AI tool loads this framework's configuration, the following content is included in the model's context:

| Content | Source | Classification |
|---------|--------|---------------|
| Framework rules and quality constraints | `.github/copilot-instructions.md`, `CLAUDE.md`, `.cursorrules` | Non-sensitive — generic engineering guidance |
| Agent definitions | `.github/agents/*.agent.md` | Non-sensitive — role descriptions and constraints |
| Skill definitions | `.github/skills/*.md` | Non-sensitive — procedural guidance |
| Path-scoped instructions | `.github/instructions/*.instructions.md` | Non-sensitive — coding standards |
| Resolved token values | All framework files post-bootstrap | May include stack names, directory paths, framework names — not credentials |
| Project anti-patterns and decisions | `docs/project-intelligence.md` | Project-specific — may include architectural context |
| Open escalations | `docs/open-handoffs.md` | Project-specific — may include current task context |

### What Must Never Be Sent

The framework's configuration files must not contain — and must never be allowed to contain — the following. The Pre-PR Security Sweep (defined in `.github/copilot-instructions.md`) enforces this before every commit.

| Category | Examples | Where It Goes Instead |
|----------|----------|----------------------|
| Credentials and secrets | API keys, OAuth client secrets, database passwords, JWT signing keys | `.env` (gitignored) — never in source |
| Production data | Database contents, user PII, transaction records | Never in source or configuration |
| Personally identifiable information | Names, emails, account IDs from real users | Never in source or configuration |
| Proprietary business logic | Pricing algorithms, internal scoring models | Not in framework files — only in application source |
| Internal infrastructure details | Internal hostnames, IP ranges, VPN endpoints | `docs/local/` (gitignored) |

**Enforcement mechanism:** The CI quality gates in `docs/templates/ci/` include a TruffleHog secret scan that runs on every push. If a secret is detected in `.github/` or tracked source files, the pipeline fails and the commit is blocked.

### Operator Setup Leakage

Documentation describing how to register an OAuth application, acquire an API key, configure a tenant, or enter provider-console settings must **never** be committed to the repository. It lives in `docs/local/` (gitignored). The only tracked record of which environment variables exist is `.env.example`, which contains placeholder values only.

Before every PR, the Pre-PR Cleanup Sweep checks for operator setup leakage. Any file under `docs/`, `README.md`, or `*_SETUP*.md` that describes provider-console steps must be moved to `docs/local/` before the PR can be merged.

---

## Researcher Agent Web Access Policy

The Researcher agent (`@researcher`) has web search capability when the underlying AI tool supports it. This capability introduces external data into the development workflow.

### What the Researcher May Search

The Researcher agent's `.github/agents/researcher.agent.md` Data Governance section defines the following allowlist. Only these query categories are permitted:

- Technology documentation and specifications (public)
- Open-source library usage patterns and changelogs
- Industry best practices and benchmarks
- CVE and security advisory databases
- Academic and engineering research (public)

### What the Researcher Must Not Search

- Internal company URLs or intranet resources
- Queries that include project-specific identifiers (project names, internal service names, proprietary domain names)
- Queries that would disclose the project's architecture or technology choices to external services

### If Your Organization Prohibits External AI Web Searches

Include the following instruction in any Researcher agent invocation:

```
Do not use web search — organizational policy restricts external AI queries. 
Base your research on the project codebase and locally available documentation only.
```

Or, for teams where web search is always prohibited, add this line to `CLAUDE.md` and `.cursorrules`:

```
# Researcher agent web search is disabled per organizational policy.
# @researcher: do not perform external web searches.
```

---

## Model Compatibility Table

The framework is designed to be model-agnostic, but AI tool capabilities affect which features work reliably. Use this table to understand capability gaps before distributing to teams.

| Capability | GitHub Copilot Enterprise | Claude Code | Cursor | Amazon Q Developer |
|-----------|:---:|:---:|:---:|:---:|
| Reads `.github/copilot-instructions.md` automatically | ✅ | ⚠️ Partial¹ | ⚠️ Via `.cursorrules`² | ❌ Requires manual reference |
| Reads `CLAUDE.md` automatically | ❌ | ✅ | ❌ | ❌ |
| Reads `.cursorrules` automatically | ❌ | ❌ | ✅ | ❌ |
| Reads `.amazonq/rules.md` automatically | ❌ | ❌ | ❌ | ✅ |
| Agent definitions (`.github/agents/`) | ✅ | ✅ Via CLAUDE.md routing | ✅ Via `.cursorrules` routing | ⚠️ Manual invocation |
| Session Start Protocol (reads project-intelligence.md) | ✅ Instructed in copilot-instructions.md | ✅ Instructed in CLAUDE.md | ✅ Instructed in .cursorrules | ⚠️ Manual instruction required |
| Verification labels | ✅ | ✅ | ✅ | ⚠️ Inconsistent |
| Researcher agent web search | ⚠️ Limited (no native web tool) | ✅ Native web search | ✅ Native web search | ⚠️ Limited |
| Long context (copilot-instructions.md size) | ~64K tokens | ~200K tokens | ~100K tokens | ~32K tokens |
| Multi-file context across `.github/` | ✅ | ✅ | ✅ | ⚠️ Partial |
| Path-scoped instructions (`applyTo`) | ✅ Native | ❌ Not supported³ | ❌ Not supported³ | ❌ Not supported³ |

**Notes:**

1. **Claude Code / copilot-instructions.md:** Claude Code does not automatically read `.github/copilot-instructions.md` as a global context file. Use `CLAUDE.md` (from `docs/adapters/CLAUDE.md.template`) to provide equivalent context. The CLAUDE.md template duplicates the non-negotiable constraints for this reason.

2. **Cursor / copilot-instructions.md:** Cursor reads `.cursorrules` for project-wide rules. The `.cursorrules` adapter in `docs/adapters/.cursorrules.template` encodes the same constraints. Cursor does not automatically read `.github/copilot-instructions.md`.

3. **Path-scoped instructions:** The `applyTo` glob mechanism in `.github/instructions/*.instructions.md` is a GitHub Copilot-specific feature. For Claude Code, Cursor, and Amazon Q, path-scoped rules must be referenced in agent definitions or user prompts when working on files in those paths. This is a known capability gap — track it in `docs/open-handoffs.md` if it affects your team's workflow.

### Minimum Viable Adapter Coverage

For a tool to be considered a supported adapter for this framework, it must implement at minimum:
- Session Start Protocol (read project-intelligence.md and open-handoffs.md)
- Non-Negotiable Constraints (verbatim, not summarized)
- Quality Gate Contract (all four gates)
- Agent routing (user can invoke named agents)

GitHub Copilot Enterprise, Claude Code (with CLAUDE.md), and Cursor (with .cursorrules) meet this minimum. Amazon Q Developer meets it only with significant manual per-session instruction.

---

## Corporate Proxy Configuration

If your organization routes AI model traffic through a corporate proxy or content filter:

### Framework File Access

The AI tools listed above read configuration files from the local repository — they do not fetch `.github/` content from external URLs at runtime. Corporate proxies that inspect outbound HTTP traffic do not affect local file reads.

However, the **AI tool itself** (Copilot, Claude, Cursor, Amazon Q) communicates with remote model APIs. Ensure these domains are allowlisted in your proxy:

| Tool | Domains to Allowlist |
|------|---------------------|
| GitHub Copilot | `copilot-proxy.githubusercontent.com`, `api.github.com` |
| Claude Code | `api.anthropic.com` |
| Cursor | `api2.cursor.sh`, `api.openai.com` (if using GPT backend) |
| Amazon Q | `q.us-east-1.amazonaws.com`, AWS regional endpoints |

### Content Filters

Corporate content filters that scan AI requests may flag:
- Large context windows containing `.github/` framework files — the files contain policy language that may match security policy keywords
- The Researcher agent's web search queries — queries about security vulnerabilities, CVEs, or authentication patterns may trigger filters

**Recommendation:** Allowlist repository-local configuration files by path pattern (`*.github/copilot-instructions.md`, `CLAUDE.md`, `.cursorrules`) if your content filter inspects prompt content. These files contain governance guidance, not sensitive data.

### Bootstrap and Upgrade Prompts

`BOOTSTRAP.prompt.md` and `upgrade.prompt.md` involve the AI reading and writing local files. These operations are local filesystem operations — they do not require external network access beyond the AI tool's normal API communication. No special proxy configuration is needed for bootstrap.

---

## Framework Governance: Who Owns `.github/`?

For organizations distributing this framework across multiple teams or repositories:

### Ownership Model

| File / Directory | Recommended Owner | Rationale |
|-----------------|-------------------|-----------|
| `.github/copilot-instructions.md` | Platform engineering team | Global constraints affect all agent behavior — changes need cross-team review |
| `.github/agents/*.agent.md` | Platform engineering team | Agent definitions encode quality standards — unilateral changes reduce consistency |
| `.github/skills/*.md` | Platform engineering team | Skills encode procedural guidance — changes should be validated across project types |
| `.github/instructions/*.instructions.md` | Platform engineering team, with team input on `applyTo` globs | Path scoping is project-specific; constraints are platform-standard |
| `.github/prompts/*.prompt.md` | Platform engineering team | Prompts are shared workflows — teams may add but not modify existing prompts |
| `docs/project-intelligence.md` | Owning team | Project-specific — teams own their own anti-patterns and decisions |
| `docs/open-handoffs.md` | Owning team | Project-specific — teams manage their own escalations |
| `CLAUDE.md`, `.cursorrules`, `.amazonq/rules.md` | Platform engineering team | Adapters must stay in sync with `copilot-instructions.md` — platform team owns sync |
| `docs/FRAMEWORK_SETUP.md` | Owning team | Project-specific bootstrap configuration |

### CODEOWNERS Template

Add to `.github/CODEOWNERS` in each project that adopts this framework:

```
# agentic-dev-framework — platform team owns framework files
.github/copilot-instructions.md     @your-org/platform-engineering
.github/agents/                     @your-org/platform-engineering
.github/skills/                     @your-org/platform-engineering
.github/instructions/               @your-org/platform-engineering
.github/prompts/                    @your-org/platform-engineering
CLAUDE.md                           @your-org/platform-engineering
.cursorrules                        @your-org/platform-engineering
.amazonq/                           @your-org/platform-engineering
docs/FRAMEWORK-SPEC.md              @your-org/platform-engineering

# Project-specific framework files — owning team
docs/project-intelligence.md        @your-org/your-team
docs/open-handoffs.md               @your-org/your-team
docs/FRAMEWORK_SETUP.md             @your-org/your-team
```

Replace `@your-org/platform-engineering` and `@your-org/your-team` with your actual GitHub team slugs.

### Framework Update Policy

When the platform team releases a new version of agentic-dev-framework:

1. Use `upgrade.prompt.md` for AI-assisted upgrades — it performs a token-aware merge that preserves project-specific values
2. Platform team announces changes via the standard team communication channel; teams use the upgrade prompt to apply
3. Changes that modify non-negotiable constraints require platform team review before distribution — they affect all adopting projects
4. Teams may not modify agent definitions, skill files, or instruction files without submitting a change upstream to the framework repository. Project-specific customization goes in `docs/project-intelligence.md` (anti-patterns and decisions), not in framework files

### Framework Version Tracking

Each bootstrapped project's `docs/FRAMEWORK_SETUP.md` records the framework version used during bootstrap. The platform team can audit version adoption by scanning for this file across repositories:

```bash
# Example: find all repos on an older framework version
# Run in your GitHub org context
gh search code "agentic-dev-framework v1.0" --owner your-org
```

Teams on versions older than N-1 should be flagged for upgrade. Teams on versions with known security-relevant changes must be prioritized.
