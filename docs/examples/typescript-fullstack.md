# Example: TypeScript / React / PostgreSQL Full-Stack Web App

> **What this shows:** A real bootstrap-to-first-PR walkthrough for a task management web app. The framework catches a missing ownership check that would have shipped to production.

---

## Project Context

**Project:** TaskFlow — a team task management web app  
**Stack:** TypeScript, React 18, Express, PostgreSQL, Prisma, Docker, Tailwind, Playwright

---

## Step 1: Bootstrap Session

The developer opens `BOOTSTRAP.prompt.md` in GitHub Copilot Chat.

**Question 1:** "TaskFlow — a team task management app where users assign tasks to teammates and track status."

**Question 2:** "TypeScript. Web application (full-stack)."

**Question 3:** "React 18 frontend, Express backend, PostgreSQL, Prisma ORM, Docker Compose, Tailwind, Playwright for E2E."

**Question 4:** "Yes, code has been started — we have a `package.json`." *(AI scans it and infers Vite, Vitest, React Query.)*

**Question 5:** "Small team (4 devs). Small Team profile."

**Question 6:** "No exclusions."

**Question 7:** "Our smoke command is `npm run test:e2e` — runs Playwright against the local stack."

**Inference pass result (partial):**

| Token | Resolved Value | Source |
|-------|---------------|--------|
| `{{PROJECT_NAME}}` | TaskFlow | Provided |
| `{{SERVER_FRAMEWORK}}` | Express | Provided |
| `{{FRONTEND_FRAMEWORK}}` | React | Provided |
| `{{DATABASE}}` | PostgreSQL | Provided |
| `{{ORM}}` | Prisma | Provided |
| `{{SMOKE_COMMAND}}` | `npm run test:e2e` | Provided |
| `{{UNIT_TEST_FRAMEWORK}}` | Vitest | Scanned from package.json |
| `{{DATA_FETCHING_LIB}}` | TanStack Query | Scanned from package.json |
| `{{CSS_FRAMEWORK}}` | Tailwind CSS | Provided |
| `{{E2E_TEST_FRAMEWORK}}` | Playwright | Provided |
| `{{A11Y_TESTING_LIB}}` | @axe-core/playwright | Inferred from Playwright |
| `{{SERVER_SOURCE_DIR}}` | `backend/src` | Scanned from directory |
| `{{CLIENT_SOURCE_DIR}}` | `frontend/src` | Scanned from directory |
| `{{FRONTEND_URL}}` | `http://localhost:5173` | Inferred from Vite |
| `{{API_URL}}` | `http://localhost:3000` | Inferred from Express default |

Developer replies: "All approved."

**Bootstrap Verification (Phase 7):**
```
### Bootstrap Verification
- ✅ Token scan: 0 unresolved tokens
- ✅ Agent count: 12 agent files match summary (12 active, 0 stubs)
- ✅ Smoke command resolved: npm run test:e2e
- ✅ FRAMEWORK_SETUP.md generated: no tokens remaining

Overall: PASS
```

---

## Step 2: First Engineer Session — Delete Task Endpoint

Three weeks in. The developer asks the Engineer agent to implement task deletion.

**Invocation:**
```
@engineer: Implement DELETE /tasks/:id — removes the task from the database.
We already have GET /tasks/:id and PATCH /tasks/:id working.
```

**Engineer produces this route (initial draft):**
```typescript
// DELETE /tasks/:id
router.delete('/:id', requireAuth, async (req, res) => {
  const { id } = req.params;
  
  await prisma.task.delete({
    where: { id }
  });
  
  res.status(204).send();
});
```

The engineer notes: "This uses `requireAuth` middleware for authentication. The route is protected."

**The framework catches it before the PR.**

The engineer, following the security instructions (`applyTo: "backend/**/*.ts"`), applies the ownership check rule:

> *"Enforce per-user ownership checks for persisted and fetched records. Return 404 for non-owned or non-existent private resources when enumeration risk exists."*

The engineer revises the implementation:

```typescript
// DELETE /tasks/:id
router.delete('/:id', requireAuth, async (req, res) => {
  const { id } = req.params;
  const userId = req.user.id;

  // Ownership check: 404 (not 403) prevents enumeration of other users' task IDs
  const task = await prisma.task.findFirst({
    where: { id, createdBy: userId }
  });

  if (!task) {
    return res.status(404).json({ error: 'Task not found' });
  }

  await prisma.task.delete({
    where: { id }
  });

  res.status(204).send();
});
```

**What was caught:** Without the ownership check, any authenticated user could delete any task by guessing (or enumerating) task IDs. The security instruction enforced this before the code even reached review — not because the engineer forgot, but because the framework made the rule inescapable in context.

---

## Step 3: First Review Council Session

Before merging the authentication PR (which included login, session management, and the user ownership pattern), the developer runs:

```
@review-council: Full review before merging the auth PR. Changes include JWT session middleware,
login/logout endpoints, and ownership checks on task CRUD endpoints.
```

**Council summary (excerpt):**

> The auth implementation is structurally sound. The JWT middleware is correctly placed and ownership checks follow the 404-not-403 pattern. The Skeptic raises one concern that the Guardian escalates: access token lifetime is set to 7 days, which is atypically long for a session token and would leave a compromised token valid for a week with no revocation path. This is a 🟠 High finding.

**Guardian finding:**
```
🟠 High | Guardian | Access token lifetime is 7 days with no revocation mechanism
File: backend/src/middleware/auth.ts, line 12
Finding: expiresIn: '7d' with no token blacklist, refresh token rotation, or logout invalidation.
A compromised token remains valid for up to 7 days.
Recommendation: Reduce to 15–30 minutes with refresh token rotation, OR implement a token
blacklist with logout invalidation. The current approach is non-compliant with session security
best practices for user-facing applications.
```

**Outcome:** The developer shortened access token lifetime to 15 minutes and added refresh token rotation before merging. The defect would have shipped without the council review.

---

## Step 4: Project Intelligence Update

After the council review, the developer runs:
```
@review-council: Update docs/project-intelligence.md with findings from the auth PR review.
```

`docs/project-intelligence.md` gets its first substantive entry:

```markdown
## Established Patterns

### Auth and Security
- Ownership check pattern: `findFirst({ where: { id, ownerId: req.user.id } })` — return 404 not 403.
  Source: Security instruction enforcement, auth PR review.
- JWT lifetime: 15-minute access token + 7-day refresh with rotation.
  Source: Council finding (auth PR) — originally 7 days, shortened to 15 min.

## Known Anti-Patterns (Do Not Use)
- `prisma.task.delete({ where: { id } })` without ownership check — allows cross-user deletion.
  Caught: auth PR, Guardian finding, corrected before merge.
```

---

## What This Example Shows

1. **The framework catches ownership vulnerabilities at implementation time** — before review, via path-scoped security instructions.
2. **The council catches session lifetime issues that implementation-time rules don't cover** — because session configuration is a design decision, not just code.
3. **Project intelligence accumulates real findings** — not aspirational rules, but actual mistakes that were caught and corrected.
4. **Bootstrap produces a complete, working configuration in one session** — no manual token editing required for a well-defined stack.
