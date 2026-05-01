# Pull Request Description Template
#
# USAGE: Copy this file to .github/PULL_REQUEST_TEMPLATE.md in your project.
# Replace {{SMOKE_COMMAND}} with your project's actual smoke command.
# (Bootstrap generates this automatically via BOOTSTRAP.prompt.md Phase 5.)
#
# This template encodes the framework's verification label system.
# Every PR must categorize what was actually verified — not assumed.

## Summary

[What this PR does and why. One paragraph. Include the user-visible outcome.]

---

## Verification Summary

For each significant file changed, select the label that describes how its behavior was confirmed. Delete labels that don't apply. At least one label required per PR.

- [ ] `VERIFIED-AUTOMATED` — A test ran, passed, and is committed. *(Specify test name or file)*
- [ ] `VERIFIED-MANUAL` — Ran a command and observed the output. *(Specify command + what you saw)*
- [ ] `ASSUMED-UNTESTED` — Change is believed correct but cannot be verified without a running environment. *(Explain why)*
- [ ] `REQUIRES-HUMAN-VERIFICATION` — Security path, auth flow, or sensitive change requiring explicit human sign-off before merge.

> **Rule:** Any `ASSUMED-UNTESTED` item on a security-critical path (auth, authorization, input validation, secret handling) automatically becomes `REQUIRES-HUMAN-VERIFICATION` regardless of confidence.
> An honest `ASSUMED-UNTESTED` report is acceptable. A false `VERIFIED-AUTOMATED` claim is a completion gate violation.

---

## Pre-Merge Checklist

- [ ] Smoke command run and passed: `{{SMOKE_COMMAND}}`
- [ ] No unresolved `{{TOKEN}}` patterns in `.github/`: `grep -r '{{' .github/ --include="*.md"`
- [ ] Council review completed (required for 🔴 High-Stakes changes: auth, schema, API contract)
- [ ] All Guardian findings either resolved or ADR filed in `docs/adr/`
- [ ] `docs/project-intelligence.md` updated if council surfaced new anti-patterns

---

## Type of Change

- [ ] `feat` — new feature
- [ ] `fix` — bug fix
- [ ] `refactor` — code change that neither fixes a bug nor adds a feature
- [ ] `docs` — documentation only
- [ ] `test` — adding or updating tests
- [ ] `chore` — tooling, CI, dependencies

---

## Related Issues / ADRs

- Closes #[issue]
- ADR: `docs/adr/ADR-[N]-[title].md` *(if architectural decision made)*
