# Project Rules — `{{PROJECT_NAME}}`

> Generated from `docs/templates/PROJECT_RULES.template.md` during bootstrap.
> Records this project's choices on the **Project Defaults** tier of constraints from `.github/copilot-instructions.md`.
> The **Universal** tier (no secrets in logs, no false-complete, no broken handoff, no operator-setup leakage, no silent security downgrades) applies regardless of what is recorded here.

---

## How To Use This File

1. During bootstrap, the AI assistant asks whether each Project Default constraint applies to your project.
2. Your answers are recorded below as **Active**, **Modified**, or **Removed** with a one-line rationale.
3. Agents read this file as part of the Standard / High-Stakes session-start protocol so they apply *your* rules, not the framework's defaults.
4. Update this file whenever the team changes its mind. Treat it as live documentation, not a one-time bootstrap artifact.

---

## Project Defaults — Recorded Decisions

| # | Default Constraint | Status | Project Rationale |
|---|---|---|---|
| 6 | No AI-generated UI | [Active / Modified / Removed] | _(why)_ |
| 7 | No copy-paste code (DRY) | [Active / Modified / Removed] | _(why)_ |
| 8 | No abandoned PRs | [Active / Modified / Removed] | _(why)_ |
| 9 | Core stack is locked | [Active / Modified / Removed] | _(why; if Modified, list the locked components)_ |
| 10 | Scripts under `{{SCRIPTS_DIR}}/` | [Active / Modified / Removed] | _(why; if Modified, name the path)_ |
| 11 | Command-first execution via `{{TASK_RUNNER}}` | [Active / Modified / Removed] | _(why)_ |
| 12 | Skills as flat files | [Active] | _(framework convention; only change if you fork the loader)_ |

---

## Additional Project-Specific Rules

Add rules here that are unique to `{{PROJECT_NAME}}` and not covered by the framework defaults. Examples:

- _"All public API endpoints must have a feature flag for the first 30 days after release."_
- _"Domain models in `domain/` may not import from `infrastructure/`."_
- _"Database queries with predicted >100ms p95 require an EXPLAIN plan in the PR description."_

> Each rule must be testable, observable, or reviewable. Aspirational rules with no enforcement path do not belong here.

---

## Change Log

| Date | Change | Decided By |
|------|--------|-----------|
| YYYY-MM-DD | Initial bootstrap | _(name)_ |
