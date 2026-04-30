---
name: "Repo Cleanup"
description: "Find and remove stale generated artifacts while preserving source, docs, and committed project assets."
argument-hint: "Describe what to clean (for example: test artifacts, old logs, temporary scripts)."
---

Clean repository artifacts safely.

Procedure:
1. Identify generated outputs (coverage, test-results, report folders, temporary logs, local scratch scripts).
2. Confirm each candidate is non-source and safe to remove.
3. Remove only stale artifacts not required as committed assets.
4. Re-run quick validation commands to ensure cleanup did not break workflows.

Return:
1. Removed items.
2. Retained items with rationale.
3. Post-cleanup validation result.
