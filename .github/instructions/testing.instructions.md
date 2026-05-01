---
applyTo: "{{SOURCE_CODE_PATHS}},{{TEST_PATHS}}"
---

<!--
  TOKEN NOTICE: Replace {{SOURCE_CODE_PATHS}} and {{TEST_PATHS}} with your
  project's actual glob patterns after running BOOTSTRAP.prompt.md.
  Example for a TypeScript web app:
    applyTo: "backend/**/*.ts,backend/tests/**/*.ts,frontend/src/**/*.ts,frontend/src/**/*.tsx,frontend/**/*.test.ts"
-->

# Testing And Verification Instructions

Use these instructions for all implementation, refactors, and bug fixes.

## Test Integrity Standard

1. Never change tests only to make them pass.
2. If a test fails unexpectedly, treat it as a potential behavior regression until disproven.
3. Update tests only when behavior is intentionally changed and the new contract is validated.
4. Preserve deterministic behavior with stable fixtures, explicit waits, and minimal mocking drift.

## Required Verification Flow

1. Run the canonical smoke workflow from repository root:
   - `{{SMOKE_COMMAND}}`
2. Run scoped backend or frontend tests for touched areas.
3. For user-facing changes, verify the observable UI outcome, not only API/network success.
4. Treat an unreachable app, broken startup path, or failing smoke workflow as an unresolved defect in the current task, not as a separate follow-up.
5. If runtime constraints block verification, report the blocker explicitly and do not claim completion.

## Completion Gate

1. Copilot must never stop at "implementation complete" while the application is broken, unreachable, or failing the smoke workflow.
2. When Copilot believes work is done, it must run the canonical smoke workflow and the relevant scoped tests before declaring success.
3. If any verification step fails, Copilot must continue iterating on the fix until the application is functional again or a concrete external blocker is identified.
4. Final status must include what was run to verify end-to-end application health, not only what code changed.

## Preferred Commands

- Full smoke: `{{SMOKE_COMMAND}}`
- Unit/integration tests: `{{UNIT_TEST_COMMAND}}`
- E2E tests: `{{E2E_TEST_COMMAND}}`

## Coverage And Scope Expectations

1. Cover success, validation, and failure paths.
2. Add or update integration/E2E tests when feature behavior is user-visible.
3. Keep coverage moving toward repository quality targets and call out gaps explicitly.

## Scope-to-Test Mapping

| Changed Area | Required Tests |
|---|---|
| `{{SERVER_SOURCE_DIR}}/routes/**` | Unit/integration CI + integration tests for affected route |
| `{{SERVER_SOURCE_DIR}}/services/**` | Unit/integration CI + service unit tests |
| `{{SERVER_SOURCE_DIR}}/middleware/**` | Unit/integration CI + middleware unit tests |
| `{{CLIENT_SOURCE_DIR}}/components/**` | Frontend CI + E2E for affected page |
| `{{CLIENT_SOURCE_DIR}}/pages/**` | Frontend CI + E2E for affected page |
| `{{CONTAINER_COMPOSE_FILE}}` | Full smoke workflow |
| `schema/` or `migrations/` | Unit/integration CI (migration test) + full smoke |
| `.github/agents/**` or `.github/skills/**` | Manual review — no automated tests |
