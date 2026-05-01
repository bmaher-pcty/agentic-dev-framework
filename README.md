# agentic-dev-framework

A portable, technology-agnostic Copilot configuration framework for agent-guided software development.

## What This Is

A complete set of `.github/` configuration files that give GitHub Copilot:
- **12 specialized agents** — each with a defined scope, constraints, and decision pattern.
- **25 domain skills** — procedural guidance for common engineering tasks.
- **Path-scoped instructions** — security, testing, branching, and review rules that activate by file pattern.
- **Reusable prompts** — council review, codebase audit, PR readiness, security review, smoke verification.

All technology-specific values are replaced with `{{TOKEN}}` placeholders so the framework works for any project: web apps, CLIs, data pipelines, microservices.

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
    # 25 skill files covering backend, frontend, infra, research, innovation, and cross-functional concerns
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

When a new version of `agentic-dev-framework` is released:

1. **Check the CHANGELOG** — Review what changed in the new version. Some changes are additive (new agents, new skills); others modify existing files.

2. **Pull the new framework files** — Do NOT re-run `BOOTSTRAP.prompt.md`. Instead, diff the new framework version against your bootstrapped `.github/` directory and selectively apply changes.

3. **Preserve your token values** — Your bootstrapped `.github/` files have actual values where the framework has `{{TOKENS}}`. Never replace your resolved values with tokens during an upgrade.

4. **Re-run `BOOTSTRAP.prompt.md --update-only`** (if supported) — Copies new files and new sections from the framework without touching your token-resolved content.

5. **Check `docs/FRAMEWORK_SETUP.md`** — Update it to reflect the new framework version after upgrading.

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
