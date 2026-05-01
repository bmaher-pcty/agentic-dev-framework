---
name: testing
description: 'Testing patterns with clear arrange-act-assert structure and coverage goals.'
---

# Testing

## When to Use
- Writing or changing services, APIs, components, and utilities.

## Procedure
1. Structure tests using Arrange / Act / Assert.
2. Cover success, validation, and failure paths.
3. Mock external dependencies deterministically.
4. Keep test data isolated via fixtures/builders.
5. When the change affects a user-facing workflow, add integration or E2E coverage for where the user sees the result.
6. If the workflow depends on runtime infrastructure, verify the closest realistic path available and call out any remaining gap explicitly.
7. Never modify or weaken tests only to make them pass.
8. Treat unexpected failures as potential product regressions until proven otherwise.
9. Run canonical smoke verification from repository root: `{{SMOKE_COMMAND}}`.

## Coverage Targets

> These per-type floors are the minimum acceptable thresholds. The framework aspiration is 90%+ across all categories. New projects should start at the floors and raise over time.

- Services: >80%
- Utilities: >90%
- API handlers: >60% (plus integration tests)

## Test Data Builder Pattern
```
// Use builders for test fixtures, not literals
// Example shown in {{UNIT_TEST_FRAMEWORK}} — adapt to your framework
function createUserFixture(overrides) {
  return {
    id: 'user-test-id',
    email: 'test@example.com',
    role: 'viewer',
    createdAt: new Date('2024-01-01'),
    ...overrides,
  };
}
```

## Mock Strategy
- Mock at the boundary (HTTP client, DB client) — not deep inside service logic.
- Use {{UNIT_TEST_FRAMEWORK}} spy/mock functions for verifying calls and replacing behavior.
- Reset mocks after each test to prevent test order dependencies.

## Checklist
- [ ] Tests are deterministic (no `Date.now()` without mocking, no random).
- [ ] Edge cases and failure paths are included, not just happy path.
- [ ] Assertions are explicit and minimal — one concern per test.
- [ ] Coverage remains at or above targets.
- [ ] User-visible outcomes are asserted for feature-critical flows.
- [ ] A change is not marked complete when only unit tests are green but workflow output is unverified.
- [ ] Required automated tests are run and passing before pushing and opening/updating a PR.
- [ ] Behavior contract changes are reflected in both implementation and tests in the same change.

## Anti-Patterns
- Testing implementation details (mocking internal function calls) — breaks on refactoring.
- `expect(something).toBeDefined()` as the only assertion — catches nothing meaningful.
- Modifying tests to make them pass without understanding why they failed.
