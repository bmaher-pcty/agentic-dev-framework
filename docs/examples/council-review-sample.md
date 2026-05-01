# Example: Council Review Sample

> **What this shows:** A real 7-perspective council review of a feature PR — not idealized output. The Skeptic and Guardian find problems; the Craftsperson finds code quality issues; the User Champion finds an invisible success state. The Advocate is honest about real strengths, not just cheerful.

---

## Context

**Project:** TaskFlow (TypeScript/React/PostgreSQL task management app — see `typescript-fullstack.md`)  
**PR:** feat/bulk-task-assignment — allows team leads to assign multiple tasks to a user in a single operation  
**Changes:** 3 new files, 2 modified files, 147 lines added, 23 deleted  
**Council depth:** Full 7-perspective (Small Team profile)

**Invocation:**
```
@review-council: Review feat/bulk-task-assignment before I merge it.
Changes: POST /tasks/bulk-assign endpoint, React bulk-select UI component,
integration tests. This touches auth middleware and the task ownership pattern.
```

---

## Council Review

### Council Summary

The bulk assignment feature is functionally correct and well-tested for the happy path. The implementation follows the established ownership pattern from the auth PR. However, the council identified three issues that require resolution before merge: a missing authorization check that allows non-leads to invoke the endpoint (🟠 High), a frontend success state that provides no user-visible confirmation (🟠 High per User Champion), and a database query that does not scale beyond ~1,000 tasks (🟡 Medium). One 🔴 Critical finding was initially raised by the Skeptic and escalated by the Guardian — see Security Assessment below.

---

### Advocate Highlights

**What this PR gets right:**

1. **Ownership pattern is consistent.** `bulkAssignTasks` in `backend/src/services/task.service.ts` (lines 34–58) uses the same `findFirst({ where: { id, createdBy: userId } })` pattern established in the auth PR. The team is building on the right foundation.

2. **Test coverage is substantive.** `tests/integration/bulk-assign.test.ts` covers the happy path (multiple tasks assigned), the partial success case (some tasks not owned by requester), and the empty input case. This is real coverage, not a token test file.

3. **The React component has correct loading and error states.** `BulkAssignModal.tsx` (lines 89–112) renders a spinner during the mutation and shows an error message on failure. The error path is not invisible.

4. **Input validation is present at both layers.** Zod schema on the route (line 12) and a service-level check for empty arrays (line 36) — this is the defense-in-depth the security instructions require.

---

### Critical Findings

| Severity | Perspective | Finding | File | Recommendation |
|----------|------------|---------|------|----------------|
| 🔴 Critical | Guardian | Endpoint accepts requests from any authenticated user — no role check for "team lead" | `backend/src/routes/tasks.ts`, line 8 | Add `requireRole('lead')` middleware before the bulk assign handler. Any user can currently mass-reassign tasks they own, including reassigning them to users outside the team. |
| 🟠 High | User Champion | Success state is invisible — modal closes with no confirmation of how many tasks were assigned | `frontend/src/components/BulkAssignModal.tsx`, line 134 | Show a toast or inline message: "5 tasks assigned to Sarah Chen." Modal closure alone is not a success signal for a bulk operation. |
| 🟠 High | Skeptic | No limit on bulk operation size — a request with 10,000 task IDs will succeed | `backend/src/routes/tasks.ts`, line 12 (Zod schema) | Add `.max(100)` to the `taskIds` array schema. Without a limit, this endpoint is a DoS vector and a slow-query trigger. |
| 🟡 Medium | Craftsperson | `bulkAssignTasks` uses a loop of individual `prisma.task.update()` calls — this is N queries for N tasks | `backend/src/services/task.service.ts`, lines 45–55 | Replace with `prisma.task.updateMany({ where: { id: { in: taskIds }, createdBy: userId } })` — one query. Current approach will noticeably slow down at >50 tasks. |
| 🔵 Low | Skeptic | The partial success response shape (`{ assigned: string[], skipped: string[] }`) is undocumented | `backend/src/routes/tasks.ts`, line 62 | Add a JSDoc comment or OpenAPI annotation. Callers can't reason about partial success without knowing the response contract. |

---

### Security Assessment

**Guardian:** The missing role check (🔴 Critical) is the highest-priority finding. The `requireAuth` middleware confirms the user is logged in; it does not confirm the user has the authority to assign tasks to teammates. In the current data model, any user who owns tasks can reassign them to any other user in the system — including users who are not on their team.

The risk: a user could use bulk assign to move tasks they own to an account they control, effectively clearing their own backlog while appearing to assign to a teammate. This is not a hypothetical — it follows directly from the missing role check and the absence of a team-membership validation.

**Required before merge:** Add role-based middleware. If `requireRole('lead')` doesn't exist yet, this PR must either implement it or be scoped down to not include the endpoint. Shipping this endpoint without the role check is a functional security gap, not a theoretical one.

The Zod input validation is correct and should not be changed. The ownership check catches task IDs the user doesn't own — this is defense in depth that should be kept even after the role check is added.

---

### UX Assessment

**User Champion:** Invisible success states in bulk operations are a category of UX defect the framework explicitly defines as a failure. The current implementation closes the modal on success — the user sees the modal disappear and must manually scan the task list to confirm assignments took effect.

For a single-record operation, modal closure might be acceptable. For a bulk operation (5, 20, 100 tasks), the user has no way to confirm:
- How many tasks were assigned vs. skipped
- Which tasks were skipped and why
- Whether the operation completed at all vs. timed out silently

The required fix is a confirmation signal before or after modal closure: a toast notification with the count ("5 tasks assigned to Sarah Chen, 2 skipped — you don't own those tasks") satisfies the requirement. The skipped-task explanation matters: users need to know the system isn't broken; it's enforcing ownership.

**Bold UX Designer:** The modal's submit button copies to a disabled state during the mutation (line 103) — this is correct and prevents double-submission. The error state renders below the submit button with sufficient contrast. No design issues beyond the success signal.

---

### Prioritized Action Items

1. **🔴 Add role check to bulk assign endpoint** — `backend/src/routes/tasks.ts`, line 8. Add `requireRole('lead')` or equivalent. Do not merge without this. [Security, Guardian]

2. **🟠 Show assignment confirmation after success** — `BulkAssignModal.tsx`, after line 134. Toast or inline: count of assigned + count skipped + reason for skips. [UX, User Champion]

3. **🟠 Cap bulk operation size in Zod schema** — `backend/src/routes/tasks.ts`, line 12. Add `.max(100)` to `taskIds` array. [Skeptic]

4. **🟡 Replace N-query loop with `updateMany`** — `backend/src/services/task.service.ts`, lines 45–55. One query, not N queries. [Performance, Craftsperson]

5. **🔵 Document partial success response shape** — JSDoc on the route handler. [Skeptic]

---

### Innovator Perspective

The framing of "bulk assign" as a single operation that either fully succeeds or partially succeeds (with skipped tasks) is reasonable. But it's worth asking: is bulk assignment the right mental model for team leads, or is it a workaround for missing sprint/milestone tooling?

If the pattern of "team lead assigns a batch of tasks at once" recurs, the product may be heading toward sprint planning features. The current implementation would scale into that — but the Architect should be consulted before bulk-assign semantics get too entrenched. If bulk assign becomes "bulk move to sprint," the ownership model may need to change (sprint membership, not individual ownership, would be the access control boundary).

This is not a blocking concern for this PR. It's a signal worth noting in project intelligence.

---

## Project Intelligence Update

**New anti-patterns discovered:**
- **Pattern:** `prisma.model.update()` in a loop over a user-supplied array → N+1 writes
  **Location:** `task.service.ts`, bulk assign loop
  **Why it's a problem:** Scales linearly with input size; single `updateMany` is available
  **Preferred alternative:** `prisma.model.updateMany({ where: { id: { in: ids } } })`

**Architectural decisions to lock:**
- Role checks belong on the route layer (middleware), not inside service functions. `requireRole()` is the pattern; service functions receive already-authorized requests.

**Coverage hot spots:**
- The partial success path in bulk operations is covered in integration tests. The role-check failure path will need a test case once `requireRole` is implemented.

**Innovator experiments proposed:**
- None initiated. Bulk-assign-to-sprint hypothesis noted for future product discussion.

---

## What This Example Shows

1. **The council finds what code review misses** — two reviewers approved this PR before the council ran. Neither noticed the missing role check. The Guardian found it because the council explicitly asks "is authorization checked at every entry point?"

2. **Invisible success states are treated as defects, not polish** — the User Champion finding is 🟠 High, not 🔵 Low. The framework's "invisible success is a defect" principle means this blocks merge.

3. **The Advocate is honest** — it describes real strengths (consistent ownership pattern, real test coverage, correct loading states) not just enthusiasm.

4. **The Innovator adds a genuinely new framing** — it doesn't repeat what other perspectives said. It asks whether the feature is solving the right problem.

5. **The Project Intelligence Update captures real findings** — the N+1 write pattern and the role-check architectural decision are things future agents can use in context.
