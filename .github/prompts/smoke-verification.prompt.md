---
name: "Smoke Verification"
description: "Run project smoke verification and report pass/fail evidence with blockers."
argument-hint: "Describe the feature area changed and any environment constraints."
---

Validate the current changes using repository-standard smoke and targeted checks.

Steps:
1. Run `{{SMOKE_COMMAND}}` from repository root.
2. If the smoke run fails, identify the first failing gate and root cause.
3. Run targeted backend/frontend tests for changed areas.
4. Summarize evidence in this format:
   - Changed area
   - Commands run
   - Pass/fail by command
   - User-visible verification status
   - Remaining blocker (if any)

Rules:
- Do not mark complete unless user-visible behavior is verified or blocker is explicit.
- Do not suggest weakening tests.
