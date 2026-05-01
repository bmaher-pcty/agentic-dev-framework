# {{PROJECT_NAME}} - Copilot Instructions

Agent-guided software development framework with 5 core agents and 8 domain skills.

## Copilot Customization Layout

- Repository-wide instructions: `.github/copilot-instructions.md`
- Path-specific instructions: `.github/instructions/*.instructions.md`
- Custom agents: `.github/agents/*.agent.md`
- Skills: `.github/skills/*.md`

**Precedence (most specific wins):** Path-scoped instructions → Agent definitions → Skills → This file.
Non-negotiable constraints (below) cannot be overridden by any source.

---

## 5 Agents

Full definitions, constraints, and decision patterns in `.github/agents/`.

| Agent | Invocation | Scope |
|-------|-----------|-------|
| Engineer | `@engineer` | Implementation, code quality, testing, delivery |
| System Architect | `@architect` | System structure, API design, data model, feature scope |
| Review Council | `@review-council` | 7-perspective code, architecture, and UX review |
| Veteran QA | `@qa` | Test plans, regression, release evidence |
| Innovator | `@innovator` | Problem reframing, alternatives, pre-commitment research |

## 8 Skills

Invoke via `#skill-name` in any agent session. Full documentation in `.github/skills/`.

| Skill | Use for |
|-------|---------|
| `#council-review` | Multi-perspective review methodology |
| `#task-triage` | 🟢/🟡/🔴 risk classification before acting |
| `#testing` | Verification patterns, coverage targets |
| `#security` | Auth, secrets, input validation rules |
| `#api-design` | REST endpoints, versioning, contracts |
| `#error-handling` | Error structures, HTTP codes, logging |
| `#pull-request-readiness` | Pre-PR gates, cleanup sweep, security review |
| `#data-modeling` | Schema design, migrations, indexes |

## Branching Workflow

See `.github/instructions/branching.instructions.md` for the full workflow and branch naming convention. These rules apply to all files (`applyTo: "**/*"`).

## Code Standards

### Commits
```
type(scope): description

feat(auth): Add JWT token refresh
fix(api): Handle null assignee in sync
test(sync): Cover partial failures
```
Commits must explain intent, scope, and impact. Vague messages are not acceptable.

### Pull Requests
- Sync to latest `main` before opening or updating a PR.
- Verify functionality in the real user-visible path before pushing.
- Run all relevant automated tests before pushing.
- Use `#pull-request-readiness` for the pre-PR cleanup and security sweep.
- Do not open a PR with known failing tests unless explicitly approved.

### Testing
See `.github/instructions/testing.instructions.md` — verification labels, completion gate, preferred commands, and scope-to-test mapping.

### Documentation
JSDoc for public APIs only — document intent and parameter semantics, not implementation details.

## Constraints (Non-Negotiable)

1. **No secrets in logs** — Mask tokens/passwords; sanitize error messages.
2. **No false-complete claims** — Work is done when it's verified in the user-visible path or the blocker is explicitly stated.
3. **No broken handoff** — If a change breaks the app, continue working until service is restored; never stop at "implemented" while non-functional.
4. **No operator setup docs in tracked folders** — Operator-specific docs (OAuth registration, API keys, tenant config) live in `docs/local/` (gitignored); `.env.example` is the only tracked record of which env vars exist.
5. **Security findings are never silently downgraded** — Guardian-class findings require documented disposition in `docs/adr/`.
6. **No AI-generated UI** — All designs manually crafted. *(Modify during bootstrap if AI-assisted UI is intentional.)*
7. **No copy-paste code** — Extract to utilities; use DRY principle.
8. **Core stack is locked** — `{{SERVER_FRAMEWORK}}`, `{{FRONTEND_FRAMEWORK}}`, `{{DATABASE}}`, `{{ORM}}`, `{{CONTAINER_RUNTIME}}`.
9. **Scripts live under `{{SCRIPTS_DIR}}/`** — No operational scripts at repo root; CI and docs reference `{{TASK_RUNNER}}` targets.
10. **Skills must be flat files** — One file per skill, `.github/skills/<name>.md`, no subfolders.

## Interpretation Gate

Before acting on any prompt, state your interpretation of the task in one or two sentences — what you understand the goal to be, what's in scope, and your intended approach. Confirm this is correct before proceeding. If something is genuinely ambiguous and cannot be inferred from the codebase, ask — but don't ask more than necessary. Surface assumptions before work begins, not after.

## Large Codebase Protocol

1. **Read contracts before implementations** — interfaces, API schemas, and tests before source files.
2. **Scope before reading** — state the target module and file(s) before any read sequence.
3. **Prefer search over full reads** — use grep/code-intelligence to locate relevant code before reading entire files.
4. **Surface scope creep** — if completing a task requires more files than scoped, announce the expansion before proceeding.

## Related Files

- [`docs/adr/`](../docs/adr/) — architecture decisions
- [`docs/templates/ROADMAP.template.md`](../docs/templates/ROADMAP.template.md) — phase planning
- [`instructions/testing.instructions.md`](instructions/testing.instructions.md) — verification gates
- [`instructions/security.instructions.md`](instructions/security.instructions.md) — auth, secrets, infrastructure rules
- [`skills/pull-request-readiness.md`](skills/pull-request-readiness.md) — pre-PR sweep
- [`agents/`](agents/) — full agent definitions
- [`skills/`](skills/) — full skill documentation

## Framework Version
agentic-dev-framework v2.0.0 ("Lean Machine")
Run `BOOTSTRAP.prompt.md` to tailor this framework to your project.
Replace `{{TOKEN}}` placeholders — see `TOKENS.md` for the full list.
