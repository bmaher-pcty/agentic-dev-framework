# agentic-dev-framework

A portable, technology-agnostic Copilot configuration framework for agent-guided software development.

## What This Is

A complete set of `.github/` configuration files that give GitHub Copilot:
- **10 specialized agents** — each with a defined scope, constraints, and decision pattern.
- **19 domain skills** — procedural guidance for common engineering tasks.
- **Path-scoped instructions** — security, testing, branching, and review rules that activate by file pattern.
- **Reusable prompts** — council review, codebase audit, PR readiness, security review, smoke verification.

All technology-specific values are replaced with `{{TOKEN}}` placeholders so the framework works for any project: web apps, CLIs, data pipelines, microservices.

## Quick Start

### 1. Copy into your project
```bash
cp -r agentic-dev-framework/.github /path/to/your-project/
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
    review-council.agent.md    # 6-perspective code review
    accessibility.agent.md     # WCAG compliance
    devops-infrastructure.agent.md
    performance.agent.md
  skills/
    # 19 skill files covering backend, frontend, infra, and cross-functional concerns
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

## The 10 Agents

| Agent | When to Use |
|-------|------------|
| **Engineer** | Implementing features, fixing bugs, verifying runtime behavior |
| **System Architect** | Designing APIs, data models, service boundaries |
| **Thoughtful Product Manager** | Scoping features, defining acceptance criteria |
| **Veteran QA** | Test planning, regression analysis, release readiness |
| **Bold UX Designer** | Visual hierarchy, accessibility, interaction design |
| **Technical Writer** | Documentation consolidation and governance |
| **Review Council** | Multi-perspective code and architecture review |
| **Accessibility** | WCAG 2.1 AA compliance and keyboard navigation |
| **DevOps/Infrastructure** | Container config, CI/CD, health checks |
| **Performance** | Bundle size, query efficiency, response time |

## Key Principles

1. **User-visible verification is mandatory** — work is not done until the user can see the outcome.
2. **No broken handoff** — if a change breaks the app, continue until it works or a blocker is proven.
3. **90%+ test coverage target** — shortfalls must be called out explicitly.
4. **No false-complete claims** — never say "done" or "implemented" if the feature isn't working end-to-end.
5. **Security is non-negotiable** — Guardian findings cannot be downgraded.
6. **Scripts live under `{{SCRIPTS_DIR}}/`** — no operational scripts at repository root.

## Token Replacement

All `{{TOKEN}}` placeholders must be replaced before the framework is useful.
See `TOKENS.md` for the full token table and run `BOOTSTRAP.prompt.md` to automate replacement.

## Framework Version
agentic-dev-framework v1.0.0
