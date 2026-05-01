# agentic-dev-framework

A portable, technology-agnostic Copilot configuration framework for agent-guided software development.

> **New to the framework? Start here: [docs/GETTING_STARTED.md](docs/GETTING_STARTED.md)**

## What This Is

A complete set of `.github/` configuration files that give GitHub Copilot:
- **12 specialized agents** — each with a defined scope, constraints, and decision pattern.
- **29 domain skills** — procedural guidance for common engineering tasks.
- **Path-scoped instructions** — security, testing, branching, and review rules that activate by file pattern.
- **Reusable prompts** — council review, codebase audit, PR readiness, security review, smoke verification.

All technology-specific values are replaced with `{{TOKEN}}` placeholders so the framework works for any project: web apps, CLIs, data pipelines, microservices.

## How Agents Actually Work (Honest Disclosure)

The framework uses the term "agent" the way the AI-coding ecosystem uses it today: **a named role with a scoped prompt, a constraint set, and recommended tool capabilities.** Agents in this repository are **Markdown prompt conventions, not runtime-enforced primitives.** They work because:

1. The model loads the agent file as context (via `.github/agents/<name>.agent.md`).
2. The user invokes the role by name (e.g., `@engineer`, `@review-council`) so the model adopts the role.
3. The constraints and decision patterns in the file shape the model's response — but **enforcement is by convention, not by a runtime gate.** No process spawns a separate agent. No tool registry blocks calls. The `recommended_capabilities:` frontmatter (formerly `tools:`) is advisory.

What this means for you:
- ✅ The verification labels, completion gate, severity rubric, and council methodology work in any compatible AI assistant (Copilot, Claude, Cursor, etc.) because they are textual rules the model follows.
- ❌ Listing `tools: [edit, execute]` in an agent file does **not** sandbox or restrict that agent at runtime. If your AI assistant grants edit/execute permissions for the session, every agent inherits them.
- 🟡 Roadmap: where a runtime *does* support real agent primitives — Claude Code Subagents, future Copilot custom chat participants — we will ship native bindings. Until then, the framework is honest about what it is: a high-quality, opinionated prompt scaffolding kit.

If your enterprise security model requires hard tool isolation per role, do not rely on this framework's frontmatter to provide it. Use your AI assistant's native permission model.

## Deployment Profiles

Choose a profile before running bootstrap. Profiles reduce coordination overhead — not quality bars.

| Profile | Team Size | Default Agents | Council Depth |
|---------|-----------|----------------|---------------|
| **Solo** | 1 developer | Engineer, Architect, Review Council, QA, Innovator | 4-perspective Micro Council |
| **Small Team** | 2–10 developers | All 12 agents active | Full 7-perspective Council |
| **Enterprise** | 10+ / regulated | All 12 agents + compliance gates | Full council + audit trail |

All profiles enforce identical quality guarantees: completion gate, security non-negotiable, no false-complete claims. To apply a profile, answer Question 5 during bootstrap ("solo" / "small team" / "enterprise").

---

## Quick Start

### 1. Copy into your project
```bash
cp -r agentic-dev-framework/.github /path/to/your-project/
cp agentic-dev-framework/BOOTSTRAP.prompt.md /path/to/your-project/
cp agentic-dev-framework/TOKENS.md /path/to/your-project/
```

### 2. Bootstrap your project
Open `BOOTSTRAP.prompt.md` in GitHub Copilot Chat and follow the prompts.
The bootstrap process replaces all `{{TOKEN}}` placeholders with your actual values.

### 3. Verify
After bootstrapping, confirm:
- `.github/copilot-instructions.md` has no `{{TOKEN}}` placeholders remaining
- Agent files reference your actual framework/library names
- `applyTo` patterns in instructions files match your directory structure

### ✅ Success Signal

Bootstrap worked if:
- `grep -r '{{' .github/ --include="*.md"` returns **no matches**
- `docs/FRAMEWORK_SETUP.md` exists and shows your project name in the header
- Your first `@engineer` invocation gets context about your actual stack, not generic token placeholders
- Bootstrap Verification (Phase 7) reported **PASS** or **PASS WITH WARNINGS** with a clear list of any manual items

## Directory Layout

```
.github/
  copilot-instructions.md      # Global agent rules and framework overview
  agents/
    engineer.agent.md          # Implementation and verification
    system-architect.agent.md  # API design and data modeling
    thoughtful-product-manager.agent.md
    veteran-qa.agent.md        # Regression and release quality
    bold-ux-designer.agent.md  # UX hierarchy and accessibility
    technical-writer.agent.md  # Documentation governance
    review-council.agent.md    # 7-perspective code review
    accessibility.agent.md     # WCAG compliance
    devops-infrastructure.agent.md
    performance.agent.md
    innovator.agent.md         # Creative problem-reframer and council voice
    researcher.agent.md        # Evidence-based multi-perspective research
  skills/
    # 29 skill files covering backend, frontend, infra, operations, research, innovation, and cross-functional concerns
  instructions/
    testing.instructions.md    # Verification gates (path-scoped)
    security.instructions.md   # Auth, secrets, infrastructure (path-scoped)
    branching.instructions.md  # Branch workflow (applies to all files)
    docker.instructions.md     # Container configuration (path-scoped)
    council-review.instructions.md
  prompts/
    council-review.prompt.md
    codebase-audit.prompt.md
    pr-readiness.prompt.md
    security-review.prompt.md
    smoke-verification.prompt.md
    repo-cleanup.prompt.md
TOKENS.md          # Token reference and bootstrap guide
BOOTSTRAP.prompt.md # Interactive bootstrap prompt
docs/
  PHILOSOPHY.md    # Framework design philosophy
```

## The 12 Agents

| Agent | When to Use | Invocation |
|-------|------------|------------|
| **Engineer** | Implementing features, fixing bugs, verifying runtime behavior | `@engineer` |
| **System Architect** | Designing APIs, data models, service boundaries | `@architect` |
| **Thoughtful Product Manager** | Scoping features, defining acceptance criteria | `@pm` |
| **Veteran QA** | Test planning, regression analysis, release readiness | `@qa` |
| **Bold UX Designer** | Visual hierarchy, accessibility, interaction design | `@designer` |
| **Technical Writer** | Documentation consolidation and governance | `@technical-writer` |
| **Review Council** | Multi-perspective code and architecture review | `@review-council` |
| **Accessibility** | WCAG 2.1 AA compliance and keyboard navigation | `@accessibility` |
| **DevOps/Infrastructure** | Container config, CI/CD, health checks | `@devops-infrastructure` |
| **Performance** | Bundle size, query efficiency, response time | `@performance` |
| **Innovator** | Reframes problems, scouts solutions, designs experiments | `@innovator` |
| **Researcher** | Evidence-based multi-perspective research for agent decisions | `@researcher` |

## Key Principles

1. **User-visible verification is mandatory** — work is not done until the user can see the outcome.
2. **No broken handoff** — if a change breaks the app, continue until it works or a blocker is proven.
3. **90%+ test coverage target** — shortfalls must be called out explicitly.
4. **No false-complete claims** — never say "done" or "implemented" if the feature isn't working end-to-end.
5. **Security is non-negotiable** — Guardian findings cannot be downgraded.
6. **The first viable solution is rarely the best solution** — before committing to an approach, ask what you'd build if you started fresh with current knowledge. The Innovator exists to enforce this question.

## Upgrading the Framework

Run `upgrade.prompt.md` in your AI assistant with your project open. Provide your current and target framework versions. The upgrade wizard:
1. Reads the CHANGELOG to classify what changed (additive, modifying, breaking, structural)
2. Produces a token-aware diff for each modified file — showing framework improvements alongside your resolved values
3. Applies additive changes automatically
4. Presents breaking and structural changes for your explicit approval before applying
5. Verifies the upgrade applied correctly and reports any regressions

> ⚠️ **Never re-run `BOOTSTRAP.prompt.md` to upgrade.** Bootstrap is for initial setup only. Re-running it will overwrite your resolved token values with generic `{{TOKEN}}` placeholders, reverting your project-specific configuration.
>
> **Principle:** Upgrading adds framework improvements. It never removes your project-specific configuration.

## Skills

| Skill | Category | Purpose |
|-------|----------|---------|
| `#static-typing-practices` | Backend | Type safety, generics, discriminated unions |
| `#api-design` | Backend | REST endpoints, versioning, error codes |
| `#error-handling` | Backend | Consistent error structures, logging strategy |
| `#security` | Backend | Input validation, secrets management, CORS |
| `#testing` | Backend | Unit test patterns, fixtures, mocking |
| `#data-modeling` | Backend | Schema design, migrations, indexes |
| `#data-migrations` | Backend | Safe schema evolution, backfill patterns |
| `#observability` | Backend | Structured logging, health checks, correlation IDs |
| `#ui-component-design` | Frontend | UI patterns, prop APIs, visual states |
| `#client-state-management` | Frontend | State stores, data fetching, cache invalidation |
| `#ui-principles` | Frontend | Accessibility, responsive design, result visibility |
| `#e2e-testing-patterns` | Frontend | E2E structure, semantic selectors, auth fixtures |
| `#async-data-fetching` | Frontend | Query key factory, optimistic updates |
| `#log-analysis-and-triage` | Frontend | Post-smoke log triage, release-gate policy |
| `#accessibility-testing` | Frontend | axe-core integration, keyboard nav, WCAG 2.1 AA |
| `#container-operations` | Infrastructure | Container security, health checks, compose |
| `#deployment` | Operations | Deployment verification, rollback procedure, blue-green/canary |
| `#secrets-rotation` | Operations | Rotation triggers, zero-downtime rotation, rotation verification |
| `#api-deprecation` | Operations | Deprecation notices, sunset headers, migration guides |
| `#db-backup-recovery` | Operations | Backup verification, PITR, RTO testing, backup storage security |
| `#internet-research` | Cross-functional | Evidence gathering with calibrated confidence |
| `#pull-request-readiness` | Cross-functional | Pre-PR quality gates |
| `#council-review` | Cross-functional | Seven-perspective review methodology |
| `#task-triage` | Cross-Functional | Risk-calibrated triage; determines Micro Council vs. full 7-perspective council depth |
| `#creative-problem-solving` | Innovation | Five Whys, SCAMPER, 10x Thinking, Pre-Mortem |
| `#solution-scouting` | Innovation | 4-tier research, 10x Solution Test, Scout Report |
| `#experimental-design` | Innovation | Hypothesis-driven spikes, time-box discipline |
| `#research-methodology` | Research | Structured research process: question scoping, source discovery, evidence collection |
| `#evidence-synthesis` | Research | Combining disparate sources into calibrated, impartial Research Briefs |

## Token Replacement

All `{{TOKEN}}` placeholders must be replaced before the framework is useful.
See `TOKENS.md` for the full token reference (with Category and Source columns) and run `BOOTSTRAP.prompt.md` to automate replacement via the 7-question smart wizard.

## Framework Version
agentic-dev-framework v1.1.0
