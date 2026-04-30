---
name: e2e-testing-patterns
description: 'E2E testing patterns for deterministic workflow testing, robust selectors, and stable assertions.'
---

# E2E Testing Patterns

## When to Use
- Verifying end-to-end user workflows and regression checks.
- Writing tests for auth flows, form submissions, and navigation.
- Adding coverage for user-visible outcomes after API mutations.

## Procedure
1. Arrange preconditions (auth/navigation) in test setup or fixtures.
2. Execute one clear user action flow per test.
3. Assert UI, URL, and data outcomes explicitly.
4. Use semantic  never CSS class selectors or positional selectors.selectors 
5. Use explicit wait  never arbitrary timeouts.conditions 
6. Isolate test data so tests don't depend on each other's state.

## Selector Priority
```
// 1. Best: role-based (accessible and readable)
getByRole('button', { name: 'Save Changes' })
getByRole('textbox', { name: 'Email address' })

// 2. Good: test ID (explicit contract)
getByTestId('sprint-plan-wizard')

// 3. Acceptable: label text
getByLabel('Password')

// 4. Avoid: CSS selectors, nth-child, class names
locator('.btn-primary:nth-child(2)')  // Fragile
```

## Auth Fixture Pattern (pseudocode)
```
// Create a reusable authenticated session fixture
authenticatedPage:
  goto('/login')
  fill Email field with test credentials
  fill Password field
  click 'Log in'
  wait for navigation to /dashboard
  yield page for use in tests
```

<details>
<summary>{{E2E_TEST_FRAMEWORK}} implementation reference</summary>

```typescript
// fixtures/authFixture.ts
export const test = base.extend<{ authenticatedPage: Page }>({
  authenticatedPage: async ({ page }, use) => {
    await page.goto('/login');
    await page.getByLabel('Email').fill('test@example.com');
    await page.getByLabel('Password').fill('TestPass123!');
    await page.getByRole('button', { name: 'Log in' }).click();
    await page.waitForURL('/dashboard');
    await use(page);
  },
});
```
</details>

## Assertion Patterns (pseudocode)
```
// Wait for navigation, not arbitrary time
expect(page).toHaveURL('/dashboard')

// Wait for element visibility
expect(heading('Dashboard')).toBeVisible()

// Assert response via network intercept
responsePromise = waitForResponse('**/api/users')
click(button('Refresh'))
response = await responsePromise
expect(response.status).toBe(200)
```

<details>
<summary>{{E2E_TEST_FRAMEWORK}} assertion reference</summary>

```typescript
// Wait for navigation, not arbitrary time
await expect(page).toHaveURL('/dashboard');

// Wait for element visibility
await expect(page.getByRole('heading', { name: 'Dashboard' })).toBeVisible();

// Assert response via network intercept
const responsePromise = page.waitForResponse('**/api/users');
await page.getByRole('button', { name: 'Refresh' }).click();
const response = await responsePromise;
expect(response.status()).toBe(200);
```
</details>

## Checklist
- [ ] Tests avoid arbitrary  use explicit wait conditions or assertions.sleeps 
- [ ] Selectors use role, label, or test  never positional or class-based.ID 
- [ ] Each test has isolated preconditions (no shared mutable state between tests).
- [ ] Failure messages are  the selector name explains what failed.actionable 
- [ ] Critical paths (login, primary workflows) are covered in CI.
- [ ] Network requests are intercepted or verified for correctness, not assumed.

## Anti-Patterns
- Arbitrary sleep/timeout  flaky, hides real wait condition.waits 
- Positional or class-based  breaks on any layout change.selectors 
- Sharing test state via global  causes test order dependencies.variables 
- Asserting only on element presence, not  misses wrong data bugs.content 
- Skipping the empty/error state in E2E  these are the most user-visible failures.tests 
