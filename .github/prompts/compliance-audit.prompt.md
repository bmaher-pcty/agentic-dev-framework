---
name: "Compliance Audit Trail"
description: "Produces a structured, human-approvable audit record of AI-assisted decisions in a session. Required for Enterprise profile teams in regulated industries."
argument-hint: "Provide the PR number or session description to audit (e.g., 'PR #142' or 'auth-refactor session 2024-01-15')."
---

# Compliance Audit Trail

## Purpose

This prompt produces a structured audit record for regulated-industry compliance. It captures what the AI agent decided, what evidence supported the decision, and what human approved it.

**Important:** This record must be reviewed and signed off by a human engineer before it counts as compliance evidence. AI-generated output alone does not satisfy compliance requirements.

**When to run this prompt:**
- Before merging any PR in an Enterprise profile project that involves security changes, auth flows, data model changes, or new integrations.
- At the end of any multi-agent session that produced architectural decisions.
- When a compliance auditor requests evidence of security review for a specific change.

**Storage:** Save the generated record to `docs/audit/AUDIT-[YYYY-MM-DD]-[PR-or-description].md`. This directory is tracked in version control for regulated industries. For non-regulated teams, audit records are optional.

**Privacy note:** Do not include code snippets, user data, or secrets in audit records. Reference file paths and line numbers only.

---

## Procedure

### Step 1 — Gather Session Context

Before generating the audit record, collect the following. Ask the user if any are unclear:

1. **PR number or session identifier** — what change set is being audited?
2. **Which agents were invoked** — list all `@agent` invocations from the session.
3. **Guardian findings from council review** — copy the Guardian section from any council review output. If no council review was run, note this explicitly.
4. **Architectural decisions made** — list any decisions that resulted in an ADR or that should have.
5. **Verification labels used** — list all `VERIFIED-AUTOMATED`, `VERIFIED-MANUAL`, `ASSUMED-UNTESTED`, and `REQUIRES-HUMAN-VERIFICATION` claims from the session.
6. **Smoke test result** — did the smoke command pass at the end of the session?

### Step 2 — Generate Audit Record

Using the context from Step 1, produce an audit record in the exact format below:

---

## Audit Record Format

```markdown
## AI Decision Audit Record

**Session ID:** [PR number or session description — e.g., "PR #142: Add OAuth token refresh"]
**Date:** [ISO 8601 date — e.g., 2024-01-15]
**Framework version:** [e.g., v1.0.0 — read from copilot-instructions.md footer]
**Profile:** [Solo / Small Team / Enterprise]
**Agents invoked:** [comma-separated list — e.g., @engineer, @review-council, @architect]

---

### Security Review Evidence

**Guardian findings from council review:**
| Severity | Finding | File / Location | Disposition |
|----------|---------|----------------|-------------|
| [🔴/🟠/🟡/🔵] | [finding description] | [file:line] | [Resolved in PR / Deferred: ADR-N / No issue] |

**Finding disposition summary:** [Resolved N / Deferred N (ADR required) / No issues found]

**Was a council review run for this change?** [Yes / No — reason if No]

**Human security reviewer:** _________________________ — **Approved:** [Yes / No / Pending]

---

### Architectural Decisions

**Decisions made in this session:**
1. [Decision description] — [ADR filed: Yes (docs/adr/ADR-N-title.md) / No (reason)]
2. [Additional decisions...]

**ADR summary:** [N ADRs filed / N decisions made without ADR (list reasons)]

**Human architecture reviewer:** _________________________ — **Approved:** [Yes / No / Pending]

---

### Test Coverage Evidence

**Coverage at session end:** [percentage, or "not measured" with reason]
**Coverage floor met:** [Yes (≥80%) / No — delta from floor: X%]
**Coverage aspiration met:** [Yes (≥90%) / No — acceptable for this change: Yes/No]

**Verification labels used in this session:**

| Claim | Label | File / Feature |
|-------|-------|---------------|
| [what was verified] | VERIFIED-AUTOMATED | [file or feature] |
| [what was verified] | VERIFIED-MANUAL | [file or feature] |
| [what was not verified] | ASSUMED-UNTESTED | [file or feature] |
| [what needs human check] | REQUIRES-HUMAN-VERIFICATION | [file or feature] |

**ASSUMED-UNTESTED on security-critical paths:** [None / List items — these automatically become REQUIRES-HUMAN-VERIFICATION]

**Human test coverage reviewer:** _________________________ — **Approved:** [Yes / No / Pending]

---

### Completion Gate Status

**Smoke test command:** [`{{SMOKE_COMMAND}}`]
**Smoke test result:** [PASS / FAIL / NOT-RUN]
**If NOT-RUN:** [reason — acceptable blockers: no running environment in CI, test environment unavailable]
**Application reachable at session end:** [Yes / No / Not applicable]

**Human sign-off:** _________________________ — **Date:** _____________

---

### Summary

**Overall compliance status:** [APPROVED / PENDING HUMAN REVIEW / BLOCKED]

- APPROVED: All sections have human sign-off and no open REQUIRES-HUMAN-VERIFICATION items.
- PENDING: Human sign-off fields are not yet complete.
- BLOCKED: One or more REQUIRES-HUMAN-VERIFICATION items are open on security-critical paths.

---

**IMPORTANT:** This document is evidence of AI-assisted review. It is NOT a substitute for human engineering judgment. A human engineer must review and sign each section above before this record has compliance value. An unsigned audit record provides no compliance protection.
```

---

### Step 3 — Store the Record

After generating the record, instruct the user:

1. Save this file as: `docs/audit/AUDIT-[YYYY-MM-DD]-[PR-or-description].md`
2. Fill in all blank signature fields with actual human reviewer names/handles.
3. Commit the completed record: `git add docs/audit/ && git commit -m "docs(audit): Add compliance audit record for [PR/session]"`
4. Link the audit record in the PR description: "Compliance audit: docs/audit/AUDIT-[date]-[description].md"

---

## Rules for This Prompt

- Do NOT generate a record that claims APPROVED status without completed human sign-off fields.
- Do NOT omit the "IMPORTANT" disclaimer at the end of the record.
- Do NOT include actual code snippets, credential values, or user data in the audit record — reference file paths and line numbers only.
- If no council review was run for the session, flag this explicitly in the Security Review Evidence section. A missing council review is not automatically a compliance failure but must be documented.
- If a Guardian finding was deferred without an ADR, flag it as a gap. Per `docs/PHILOSOPHY.md`, deferring a security finding requires documented justification.
- Surface all `ASSUMED-UNTESTED` items that touch auth, authorization, input validation, or secret handling — these automatically become `REQUIRES-HUMAN-VERIFICATION`.
