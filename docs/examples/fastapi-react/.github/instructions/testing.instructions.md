---
applyTo: "backend/**/*.py,backend/tests/**/*.py,frontend/src/**/*.ts,frontend/src/**/*.tsx,frontend/src/**/*.test.ts"
---

# Testing And Verification Instructions

Use these instructions for all implementation, refactors, and bug fixes.

## Test Integrity Standard

1. Never change tests only to make them pass.
2. If a test fails unexpectedly, treat it as a potential behavior regression until disproven.
3. Update tests only when behavior is intentionally changed and the new contract is validated.
4. Preserve deterministic behavior with stable fixtures, explicit waits, and minimal mocking drift.

## Required Verification Flow

1. Run the canonical smoke workflow: `make smoke`
2. Run scoped backend or frontend tests for touched areas.
3. For user-facing changes, verify the observable UI outcome, not only API/network success.
4. Treat an unreachable app, broken startup path, or failing smoke workflow as an unresolved defect.
5. If runtime constraints block verification, report the blocker explicitly and do not claim completion.

## Preferred Commands

- Full smoke: `make smoke`
- Backend unit/integration tests: `pytest backend/tests/`
- Frontend unit tests: `npm --prefix frontend run test`
- E2E tests: `npx playwright test`
- A11y tests: `npx playwright test --grep @a11y`

## Completion Gate

1. Never stop at "implementation complete" while the application is broken or failing `make smoke`.
2. Run `make smoke` and scoped tests before declaring success.
3. Final status must include what was run to verify end-to-end application health.

## Coverage Expectations

1. Cover success, validation, and failure paths.
2. Add or update integration/E2E tests when feature behavior is user-visible.
3. Target: 90%+ coverage for backend services; 80%+ for route handlers.

## Scope-to-Test Mapping

| Changed Area | Required Tests |
|---|---|
| `backend/src/routes/**` | pytest integration tests for affected route |
| `backend/src/services/**` | pytest unit tests for service |
| `backend/src/middleware/**` | pytest unit tests for middleware |
| `frontend/src/components/**` | Frontend unit tests + Playwright E2E |
| `frontend/src/pages/**` | Frontend unit tests + Playwright E2E |
| `docker-compose.yaml` | Full `make smoke` |
| `backend/alembic/` | pytest migration test + full smoke |
| `.github/agents/**` | Manual review — no automated tests |
