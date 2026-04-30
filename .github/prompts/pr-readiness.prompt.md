---
name: "PR Readiness"
description: "Check if a branch is ready for PR using project quality and verification gates."
argument-hint: "Optionally specify the branch and scope to validate."
---

Evaluate PR readiness for this repository.

Required gates:
1. Changed files are scoped and intentional.
2. Relevant tests pass, including `{{SMOKE_COMMAND}}`.
3. User-visible behavior is verified where applicable.
4. No false-complete claims; blockers are explicit.
5. Documentation and instruction references are current.

Output:
1. Readiness decision: ready or not ready.
2. Gate-by-gate pass/fail.
3. Missing evidence or blockers.
4. Final commit and PR checklist.
