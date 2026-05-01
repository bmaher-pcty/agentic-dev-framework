---
name: pull-request-readiness
description: 'Pre-PR quality gates for branch sync, commit quality, verification evidence, and test completion before push/opening a pull request.'
---

# Pull Request Readiness

## When to Use
- Any time code will be pushed or a pull request will be opened/updated.

## Required Sequence
1. Sync your branch with the latest `main` before opening or updating a PR.
2. Resolve merge conflicts locally and re-run verification after conflict resolution.
3. Verify the user-visible workflow manually (or closest realistic runtime path) before pushing.
4. Run all required automated tests before pushing.
5. Prepare clear commit messages that explain intent and impact.
6. Open PR with a descriptive title and detailed body including verification evidence.

## Commit Best Practices
- Use `type(scope): description` format.
- Keep commits focused and logically grouped.
- Explain why the change exists and what behavior changed.
- Avoid vague messages such as `fix stuff` or `updates`.

## PR Title Best Practices
- Be specific and outcome-oriented.
- Include scope and user/system impact.
- Prefer explicit titles such as `feat(reporting): persist report executions and surface latest results in UI`.

## PR Description Minimum Content
- Problem being solved and context.
- What changed (backend, frontend, data model, docs, security).
- Verification steps and observed outcomes.
- Tests executed and pass/fail summary.
- Known limitations, blockers, or follow-up tasks.

## Pre-Push / Pre-PR Gate
- Latest `main` merged or rebased into your branch.
- Functionality verified in the real user path (success + failure states).
- Relevant automated tests completed and passing.
- Documentation updated where behavior/contracts changed.
- No false-complete claims; blockers are explicitly documented.

## Suggested Commands
```bash
git fetch origin
git merge origin/main

{{TEST_COMMAND}}
{{SMOKE_COMMAND}}
```

## Checklist
- Branch contains latest `main`.
- Commits are specific, readable, and useful for review history.
- PR title is clear and descriptive.
- PR body includes meaningful implementation and verification details.
- Functionality verified before push/PR.
- Required tests run before push/PR.

---

## Pre-PR Cleanup Sweep (Always)

Before pushing the final commit on a feature branch, perform a cleanup sweep on the diff against `main`:

1. **Stale-attempt scan**: Remove code left over from earlier approaches in the session that did not become the chosen solution. Watch for: dead imports, unused exports, helper functions never called, two parallel implementations where only one is wired up, commented-out blocks, stub TODOs, debug `console.log` lines, temporary feature flags, and migration files for schemas that were redesigned.
2. **Reference integrity**: Confirm every imported symbol is actually used and every export is consumed somewhere (production code or tests). If an export exists only because tests assert on it, document that with a brief JSDoc note.
3. **Duplicated logic**: Look for near-identical blocks across the diff that should be deduplicated into a shared helper. Resolve them inside the same PR rather than deferring.
4. **Doc/script drift**: If the PR rewrites a flow, remove docs and scripts that describe the old flow. Markdown that points to deleted endpoints or removed UI is a defect.
5. **Spec vs implementation drift**: Confirm every new type/interface matches how its callers actually use it. Remove fields no caller reads.
6. **Operator setup leakage**: Any new file under `docs/`, `RUN.md`, or `*_SETUP*.md` at the repo root that describes provider-console clicks, API-key acquisition, OAuth app registration, or tenant-specific values must be moved to `docs/local/` (gitignored).

Use sub-agents in parallel for this sweep (one for dead-code, one for security/secrets) so the cleanup and security review happen together. Treat any finding as in-scope for the same PR.

---

## Pre-PR Security Review (Sensitive-Data Sweep)

Before pushing the final commit, run a security sweep on the diff:

1. **No secrets in source, tests, fixtures, docs, or migrations** — only `.env.example` placeholders are allowed in version control.
2. **No raw credentials, tokens, or auth headers in log calls** — confirm redaction on every new log line that touches credentialed paths.
3. **All new endpoints require authentication middleware** and enforce per-user ownership for any record reads/writes/deletes.
4. **All user-supplied input that flows into external API calls is validated and escaped** at both the route layer and the service layer (defense in depth). Anything interpolated into a query string, header, URL path, or shell command must be whitelisted to safe characters before use.
5. **No PII in new log lines** that did not appear before this PR.

Run this with a sub-agent so review happens in parallel with implementation rather than after.
