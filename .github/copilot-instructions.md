# {{PROJECT_NAME}} - Copilot Instructions

Agent-guided software development framework with 12 specialized agents and 25 domain skills.

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

25 skills across 6 categories — Backend, Frontend, Infrastructure, Cross-Functional, Innovation, Research. Invoke via `#skill-name` in any agent session. Full documentation in `.github/skills/`.

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

## Constraints (Non-Negotiable)

> **Constraint Reliability Note:** These constraints encode engineering judgment that AI agents should follow. They are guidance for AI behavior, not programmatic enforcement. When an agent appears to violate a constraint, treat it as a signal to invoke the Review Council or check the session manually — not as a framework failure. The human in the loop is the ultimate enforcement mechanism.

1. **No AI-generated UI** — All designs manually crafted, `{{CSS_FRAMEWORK}}` base.
2. **No copy-paste code** — Extract to utilities; use the DRY principle.
3. **No abandoned PRs** — Features ship or get reverted; no "draft" PRs.
4. **No secrets in logs** — Mask tokens/passwords; sanitize error messages.
5. **Core stack is locked** — `{{SERVER_FRAMEWORK}}`, `{{FRONTEND_FRAMEWORK}}`, `{{DATABASE}}`, `{{ORM}}`, `{{CONTAINER_RUNTIME}}`.
6. **Scripts live under `{{SCRIPTS_DIR}}/`** — No operational scripts at repository root.
7. **Command-first execution only** — Use `{{TASK_RUNNER}} <target>` instead of invoking scripts directly.
8. **Skills must be flat files** — Store each skill as `.github/skills/<skill_name>.md`; do not use per-skill subfolders.
9. **No false-complete claims** — Do not say work is done unless the feature is working in the user-visible path, the application is still reachable and passing required verification, or the remaining blocker has been explicitly surfaced.
10. **No broken handoff** — If a change leaves the app broken, degraded, or unreachable, continue working until service is restored and verification passes; never stop at "implemented" while the application is non-functional.
11. **No operator setup docs in tracked folders** — See "Local-Only Setup Docs" below.

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
agentic-dev-framework v1.0.0
Run `BOOTSTRAP.prompt.md` to tailor this framework to your project.
