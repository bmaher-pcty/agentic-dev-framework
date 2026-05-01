---
name: "Framework Health Check"
description: "Diagnose framework configuration health. Checks for unresolved tokens, project-intelligence population, stale council reviews, and inactive agent stubs. Run whenever agent responses feel generic, off-topic, or when you suspect bootstrap didn't complete correctly."
argument-hint: "Run with no arguments for a full health check. Or specify 'tokens', 'intelligence', 'agents', or 'staleness' to check one area."
user-invocable: true
---

# Framework Health Check

## Purpose

Run this prompt when:
- Agent responses feel generic or ignore your project's actual stack.
- Bootstrap ran but you're not sure all tokens resolved.
- It's been more than 30 days since a council review.
- A new team member joins and you want to verify the framework is properly configured.
- The CI token check is failing but you don't know which files are affected.

## Procedure

Run the following five checks and produce a structured report. For each check, state the status and provide the detail that led to that conclusion.

---

### Check 1 — Unresolved Tokens

```bash
grep -r '{{' .github/ --include="*.md" \
  | grep -v '<!--' \
  | grep -v 'TOKENS.md' \
  | grep -v 'BOOTSTRAP.prompt.md' \
  | head -30
```

- **PASS** — No matches found.
- **FAIL** — Matches found. List each file and the unresolved token(s). Remediation: run `BOOTSTRAP.prompt.md` or replace tokens manually using `TOKENS.md` as reference.

---

### Check 2 — Project Intelligence Population

Read `docs/project-intelligence.md`.

- **FAIL** — File does not exist. Remediation: create it from the template in `docs/project-intelligence.md` (in the framework repo) or run `@review-council` which populates it automatically.
- **WARN** — File exists but all rows contain only placeholder content (rows starting with `_Example:` or `*(No patterns documented yet.)*`). The framework is installed but has never been used for a real session. Remediation: run a council review and use `.github/prompts/post-mortem.prompt.md` to capture findings.
- **WARN** — Front-matter shows `anti_pattern_count: 0` and `locked_decision_count: 0`. File was created but not populated after council reviews. Remediation: run `@review-council` on a recent PR and capture findings.
- **PASS** — File exists and contains at least one real entry (non-placeholder) in at least two sections.

---

### Check 3 — Agent Stub Files

```bash
grep -rl "was deactivated at bootstrap" .github/agents/
```

- **PASS** — No deactivated stubs found (all agents are active or the project intentionally has no stubs).
- **WARN** — Deactivated stubs found. List them. Note: stubs are expected for agents deactivated during bootstrap (e.g., UX Designer for a CLI project). WARN only if stubs exist for agents that should be active for this project type. Remediation: restore content from the framework repository or confirm the deactivation is intentional.

---

### Check 4 — Framework Setup Completeness

Check if `docs/FRAMEWORK_SETUP.md` exists.

- **PASS** — File exists and contains your project name and active agent list.
- **WARN** — File does not exist. Bootstrap may not have completed fully. Remediation: re-run `BOOTSTRAP.prompt.md` or manually create `docs/FRAMEWORK_SETUP.md` from `docs/templates/FRAMEWORK_SETUP.md.template.md`.
- **WARN** — File exists but still contains `{{TOKEN}}` patterns. Some tokens were not resolved. Remediation: resolve remaining tokens manually.

---

### Check 5 — Council Review Staleness

```bash
git log --oneline --follow -- docs/project-intelligence.md | head -5
```

- **PASS** — `project-intelligence.md` has been committed within the last 30 days.
- **WARN** — Last commit to `project-intelligence.md` was more than 30 days ago, or no commits exist. Staleness means the file may not reflect current codebase patterns. Remediation: schedule a council review in the next sprint.
- **WARN** — `git log` returns no commits at all for this file. The file was never updated after initial creation. This is the highest-priority remediation item.

---

## Output Format

```markdown
## Framework Health Report — [date]

| Check | Status | Detail |
|-------|--------|--------|
| Token resolution | PASS/FAIL/WARN | [specific finding] |
| Project intelligence | PASS/FAIL/WARN | [specific finding] |
| Agent stubs | PASS/FAIL/WARN | [specific finding] |
| Framework setup | PASS/FAIL/WARN | [specific finding] |
| Council staleness | PASS/FAIL/WARN | [specific finding] |

**Overall: PASS / PASS WITH WARNINGS / FAIL**
```

If any check is FAIL or WARN, add a numbered **Remediation Steps** section:
1. [Most critical item first, with exact command or action]
2. [Next item]
...

If overall is PASS: "Framework is healthy. No action required."

## Severity of Overall Status

- **FAIL** — One or more FAIL checks. Framework is misconfigured or incomplete. Address before relying on agent responses.
- **PASS WITH WARNINGS** — No FAIL checks, but WARN items exist. Framework is functional but not fully optimized. Address warnings when convenient.
- **PASS** — All checks pass. Framework is correctly configured.
