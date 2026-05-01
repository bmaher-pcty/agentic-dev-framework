# {{PROJECT_NAME}} - Copilot Instructions

Agent-guided software development framework with 12 specialized agents and 29 domain skills.

## Copilot Customization Layout

Keep Copilot customizations under `.github/`:
- Repository-wide instructions: `.github/copilot-instructions.md`
- Path-specific instructions: `.github/instructions/*.instructions.md`
- Custom agents: `.github/agents/*.agent.md`
- Agent skills (tooling guidance): `.github/skills/*.md`

## Instruction Precedence (Most Specific Wins)

When guidance conflicts between different sources, apply in this order:

1. **Path-scoped instructions** (`.github/instructions/*.instructions.md` with matching `applyTo`) — highest authority for files in their scope.
2. **Agent definitions** (`.github/agents/*.agent.md`) — highest authority for agent-specific behavior when that agent is active.
3. **Skills** (`.github/skills/*.md`) — procedural guidance when explicitly invoked.
4. **This file** (`copilot-instructions.md`) — baseline defaults and global rules.

If a path-scoped instruction contradicts this file, the path-scoped instruction wins for files matching its `applyTo` pattern. If an agent's constraints contradict a skill's procedure, the agent's constraints win.

**Non-negotiable constraints** (Section "Constraints (Non-Negotiable)" below) represent the highest-priority guidance. Agents must not override them; if a constraint conflicts with a task requirement, escalate to the human reviewer.

---

## 12 Agents (Roles)

Full agent definitions, constraints, and decision patterns are in `.github/agents/`. Invocation examples by project type are in [`docs/AGENT-INVOCATIONS.md`](../docs/AGENT-INVOCATIONS.md).

| Agent | Invocation | Scope |
|-------|-----------|-------|
| Thoughtful Product Manager | `@pm` | Feature fit, roadmap, acceptance outcomes |
| System Architect | `@architect` | System structure, API design, data model |
| Engineer | `@engineer` | Implementation, code quality, testing, delivery |
| Veteran QA | `@qa` | Test plans, regression, release evidence |
| Bold UX Designer | `@designer` | Visual hierarchy, typography, result discoverability |
| Technical Writer | `@technical-writer` | Doc governance, consolidation, canonical ownership |
| Review Council | `@review-council` | 7-perspective code, architecture, UX review |
| Accessibility | `@accessibility` | WCAG 2.1 AA, keyboard nav, screen reader compat |
| DevOps/Infrastructure | `@devops-infrastructure` | Containers, CI/CD, deployment health |
| Performance | `@performance` | Bundle size, query efficiency, response times |
| Innovator | `@innovator` | Problem reframing, solution scouting, experiments |
| Researcher | `@researcher` | Evidence gathering, multi-perspective research |

## Domain Skills

29 skills across 7 categories — Backend, Frontend, Infrastructure, Operations, Cross-Functional, Innovation, Research. Invoke via `#skill-name` in any agent session. Full documentation in `.github/skills/`.

**Operations Skills**
- `#deployment` — deployment verification protocol, rollback procedure, blue-green/canary patterns
- `#secrets-rotation` — rotation triggers, zero-downtime rotation, rotation verification
- `#api-deprecation` — deprecation notices, sunset headers, migration guides, breaking change policy
- `#db-backup-recovery` — backup verification (not just creation), PITR, RTO testing, backup storage security

## Agent Invocation

For full per-agent invocation examples across web apps, CLI/service, and data pipeline project types, see [`docs/AGENT-INVOCATIONS.md`](../docs/AGENT-INVOCATIONS.md).

## Development Phase Structure

Define your project's phases in `docs/ROADMAP.md`. A template with common phase types (Scaffolding, Core Domain, Integration, Observability, Hardening) is available at `docs/templates/ROADMAP.template.md`.

Agents reference this document for roadmap context. Keep it current.

## Branching Workflow

See `.github/instructions/branching.instructions.md` for the full branching workflow, branch naming convention, and session start checklist. These rules apply to all files (`applyTo: "**/*"`).

## Code Standards

### Commits
```
type(scope): description

feat(auth): Add JWT token refresh
fix(api): Handle null assignee in sync
docs(db): Add schema diagram
test(sync): Cover partial failures
```

- Commits must be descriptive and useful to reviewers: explain intent, scope, and impact.
- Avoid vague commit messages that do not communicate behavior change.

### Escalation Protocol

When an agent cannot resolve a decision alone: write an SBAR entry to `docs/open-handoffs.md` (Situation, Background, Assessment, Decision needed), set status `OPEN`, name the receiving agent, and do NOT proceed until resolved. The receiving agent reads this file at session start. Mark `RESOLVED` when complete.

### Pull Requests
- Merge latest `main` into your branch before opening or updating a PR.
- Verify functionality in the real user-visible path before pushing and opening a PR.
- Run all relevant automated tests before pushing and before opening a PR.
- Use a clear, detailed PR title and description that include scope, rationale, and verification evidence.
- Do not open a PR with known failing tests unless explicitly approved and documented as blocked.

See `.github/skills/pull-request-readiness.md` for the full pre-PR cleanup sweep and security review procedures.

### Testing

See `.github/instructions/testing.instructions.md` for test integrity standards, required verification flow, preferred commands, coverage expectations, and scope-to-test mapping. That file applies to all source and test files.

### Documentation
JSDoc for public APIs only — document intent and parameter semantics, not implementation details.

## Constraints

Two tiers. The **Universal** tier is non-negotiable across every project that adopts this framework — these encode the framework's safety and honesty guarantees. The **Project Defaults** tier is opinionated guidance the original framework authors found valuable; adopting projects should review, keep, modify, or remove items here during bootstrap and record their choices in `docs/templates/PROJECT_RULES.template.md` (or equivalent).

> **Constraint Reliability Note:** All constraints — Universal and Project Default — are guidance for AI behavior, not programmatic enforcement. When an agent appears to violate a constraint, treat it as a signal to invoke the Review Council or audit the session manually. The human in the loop is the ultimate enforcement mechanism.

### Universal (Non-Negotiable)

These map to the five tenets in `docs/PHILOSOPHY.md`. Removing any of them removes the framework's value proposition.

1. **No secrets in logs** — Mask tokens/passwords; sanitize error messages.
2. **No false-complete claims** — Do not say work is done unless the feature is working in the user-visible path, the application is still reachable and passing required verification, or the remaining blocker has been explicitly surfaced.
3. **No broken handoff** — If a change leaves the app broken, degraded, or unreachable, continue working until service is restored and verification passes; never stop at "implemented" while the application is non-functional.
4. **No operator setup docs in tracked folders** — See "Local-Only Setup Docs" below.
5. **Security findings are never silently downgraded** — Guardian-class findings require explicit, documented disposition.

### Project Defaults (Adjust During Bootstrap)

Keep, modify, or remove these to match your team's reality. Each is a reasonable default, not a universal truth.

6. **No AI-generated UI** — All designs manually crafted, `{{CSS_FRAMEWORK}}` base. *(Skip if you intentionally use AI-assisted UI generation with human review.)*
7. **No copy-paste code** — Extract to utilities; use the DRY principle. *(Adjust threshold to fit your team's tolerance.)*
8. **No abandoned PRs** — Features ship or get reverted; no long-lived "draft" PRs. *(Modify timeline expectations to match your release cadence.)*
9. **Core stack is locked** — `{{SERVER_FRAMEWORK}}`, `{{FRONTEND_FRAMEWORK}}`, `{{DATABASE}}`, `{{ORM}}`, `{{CONTAINER_RUNTIME}}`. *(Remove or rewrite if your project explicitly supports stack experimentation.)*
10. **Scripts live under `{{SCRIPTS_DIR}}/`** — No operational scripts at repository root. *(Skip if your ecosystem expects scripts elsewhere — e.g., `bin/`, `Justfile` at root.)*
11. **Command-first execution only** — Use `{{TASK_RUNNER}} <target>` instead of invoking scripts directly. *(Skip if you do not use a task runner.)*
12. **Skills must be flat files** — Store each skill as `.github/skills/<skill_name>.md`; do not use per-skill subfolders. *(This is a framework convention; only change if you fork the skill loader.)*

## Local-Only Setup Docs

Operator-specific documentation (OAuth registration, API keys, tenant configuration) must **never** be committed. It lives in `docs/local/` (gitignored). `.env.example` is the only tracked record of which env vars exist; values and acquisition steps stay in `docs/local/`. Before any PR, audit the diff and move any leaked setup steps to `docs/local/`.

## Repository Standards (Always Enforced)

1. All shell automation in `{{SCRIPTS_DIR}}/`; docs and CI reference `{{TASK_RUNNER}}` targets, not script paths.
2. Skills are one-file-per-skill in `.github/skills/` with kebab-case names ending `.md`.
3. Any deviation from framework standards requires an ADR in `docs/adr/`.
4. Existing automated tests must pass for all completed work; test integrity is protected — do not rewrite/weaken tests without user approval.
5. QA agents must complete a full regression run after each session.
6. Coverage: 80% floor, 90% aspiration. See `.github/skills/testing.md` for per-type targets. Shortfalls below floor must be called out explicitly.
7. User-visible verification is mandatory — a workflow is not complete until the observable result is confirmed.
8. Blocked is better than wrong — state blockers plainly; do not guess or continue on a broken path.
9. Quality bars are additive — functionality, usability, security, and accessibility never regress without an explicit user decision.

## Large Codebase Protocol

For repositories with extensive codebases, agents must follow these navigation constraints to work effectively within context window limits:

1. **Read contracts before implementations.** Always read interface files, API schemas, and test files before reading implementation files. The contract tells you what to expect; the implementation confirms it. Reading implementations first without knowing the contract wastes context and produces lower-quality results.
2. **Scope before reading.** State the target module, service, and specific file(s) before beginning any read sequence. Do not speculatively read files that may not be relevant. Every file read consumes context that cannot be recovered — scope the problem first.
3. **Use `docs/project-intelligence.md` as the first read.** Anti-patterns, locked architectural decisions, and coverage hot spots in the intelligence file directly constrain implementation choices. Read it before reading source code. Reproducing a known anti-pattern is a framework violation even if the implementation is otherwise correct.
4. **Prefer grep over full reads.** Use file search (grep, ripgrep, code intelligence) to locate relevant code patterns before reading entire files. Read only the sections containing the relevant code. A targeted grep is faster than a full file read and leaves more context for implementation.
5. **Surface scope creep explicitly.** If completing a task requires reading more files than initially scoped, surface the expanded scope to the user before proceeding. Never silently expand the reading scope. Unannounced scope expansion makes work unpredictable and can pull in unrelated concerns.

## Environment Setup

See [`docs/ENVIRONMENT.md`](../docs/ENVIRONMENT.md) for prerequisites, quick start commands, and useful development commands.
Replace all `{{TOKEN}}` placeholders in that file after running BOOTSTRAP.prompt.md.

## Related Files

- [`docs/AGENT-INVOCATIONS.md`](../docs/AGENT-INVOCATIONS.md) — per-agent invocation examples by project type
- [`docs/ENVIRONMENT.md`](../docs/ENVIRONMENT.md) — prerequisites, quick start, useful commands
- [`docs/PHILOSOPHY.md`](../docs/PHILOSOPHY.md) — why the framework's rules exist
- [`docs/adr/`](../docs/adr/) — architecture decisions
- [`instructions/testing.instructions.md`](instructions/testing.instructions.md) — verification gates and coverage targets
- [`instructions/security.instructions.md`](instructions/security.instructions.md) — auth, secrets, infrastructure rules
- [`skills/pull-request-readiness.md`](skills/pull-request-readiness.md) — pre-PR cleanup sweep and security review
- [`agents/`](agents/) — full agent definitions, constraints, and decision patterns
- [`skills/`](skills/) — full skill documentation

## Custom Agent Tool Profiles

Tool profiles are defined in each agent file. Quick reference: Engineer and Veteran QA use `execute` in addition to `read, search, edit, todo`. Innovator and Researcher use `web`. Technical Writer and Review Council omit `execute`.

## Framework Version
agentic-dev-framework v1.1.0
Run `BOOTSTRAP.prompt.md` to tailor this framework to your project.
