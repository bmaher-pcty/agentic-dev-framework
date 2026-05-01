---
name: Task Triage
description: Classify tasks by risk level to determine appropriate council depth before review.
---

# Task Triage

## When to Use

Invoke at the start of every council review, before selecting which perspectives to activate. The triage classification determines review depth — not quality bars. The Guardian perspective runs at full depth on **all triage levels**.

## Classification Rubric

### 🟢 Trivial
- ≤3 files changed
- No auth, authorization, or session surfaces touched
- No database schema changes
- No new API endpoints or contract changes
- No configuration changes (environment, CI, infrastructure)
- No security-relevant dependencies added or updated

**Triggers:** 4-perspective Micro Council — Guardian + Craftsperson + User Champion + Advocate

---

### 🟡 Standard
- Multi-file feature work not touching security surfaces
- New components, pages, or service methods
- Test additions or updates
- Documentation changes
- Dependency updates for non-security packages

**Triggers:** Full 7-perspective council

---

### 🔴 High-Stakes
- Auth, authorization, or session handling changes
- Database schema changes (migrations, model changes)
- API contract changes (new endpoints, breaking changes, auth requirements)
- External integrations (OAuth, webhooks, third-party APIs)
- Secret handling (new env vars, key rotation, credential storage)
- Security-relevant dependency changes
- Infrastructure changes (container config, proxy rules, CI gates)

**Triggers:** Full 7-perspective council + explicit Innovator activation + Guardian deep-dive

---

## The Guardian-Always Rule

The Guardian perspective runs at **full depth on all triage levels**. Triage never reduces security scrutiny. A 2-line change to an auth middleware is High-Stakes regardless of file count.

## Micro Council (Trivial tasks)

Run perspectives in this order:
1. **Advocate** — What's strong about this change?
2. **Guardian** — Any security, integrity, or quality bar concerns? (Full depth)
3. **Craftsperson** — Pattern consistency and maintainability?
4. **User Champion** — Developer / end-user experience impact?

Skip the Skeptic, Synthesizer (use brief summary instead), and Innovator for Trivial tasks. If any Guardian finding is 🟠 High or 🔴 Critical, immediately escalate to Standard or High-Stakes council.

## Checklist

Before classifying as Trivial, confirm ALL of the following:
- [ ] No files in auth/, security/, middleware/ directories modified
- [ ] No database migration files added or changed
- [ ] No environment variable names added or changed
- [ ] No API route signatures changed
- [ ] No `{{CONTAINER_COMPOSE_FILE}}` or `{{CONTAINER_RUNTIME}}` configuration changed
- [ ] No third-party SDK or auth library version changed

If any item is checked, reclassify as Standard or High-Stakes.

## Anti-Patterns

- **Classifying as Trivial to skip Guardian review** — The Guardian always runs. There is no triage level that bypasses security review.
- **Using file count alone** — A 1-line change to `auth/middleware.ts` is High-Stakes. File count is an input, not a decision.
- **Skipping triage** — Always run triage before selecting council depth. Even obvious cases benefit from explicit classification.
