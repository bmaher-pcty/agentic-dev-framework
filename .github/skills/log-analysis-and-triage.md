---
name: log-analysis-and-triage
description: 'QA hidden-problem log analysis workflow for post-smoke validation, triage, and release gating.'
---

# Log Analysis and Triage

## When to Use
- After smoke tests for every change set.
- During release signoff and regression triage.
- When tests pass but behavior still looks unstable.

## Procedure
1. Gather recent logs for all running services (api, web, db, workers).
2. Count errors, warnings, and critical patterns.
3. Categorize findings by auth, database, network, and style/build issues.
4. Produce a concise report and attach samples for triage.
5. Fail the gate when critical patterns are present.
6. Cross-check logs against the actual user-visible workflow so "green tests, broken feature" is treated as a process failure.

## Critical Patterns
- `fatal`, `panic`, `unhandled`
- `econnrefused`, `connection refused`
- `module not found`, `parse error`, `adjacent jsx`
- database errors, ORM-level errors

<details>
<summary>{{ORM}}-specific patterns</summary>

- `prisma.*error`
- `database error`
- `PrismaClientKnownRequestError`
- `PrismaClientUnknownRequestError`
</details>

## QA Decision Rules
- `critical > 0`: fail build/release gate.
- `errors > 0` and `critical = 0`: warn and create follow-up issue.
- `warnings only`: annotate test report and monitor trends.

## Output Expectations
- Machine-readable summary (e.g., JSON) for CI decisions.
- Human-readable summary for QA triage.
- Top critical log samples included for debugging handoff.

## Checklist
- Smoke tests ran before log review.
- Logs include all running service types.
- Report artifacts are generated and attached.
- Release gate decision documented.
- Hidden runtime failures are compared against what the user sees in the UI.
