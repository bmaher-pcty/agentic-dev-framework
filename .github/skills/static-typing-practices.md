---
name: static-typing-practices
description: 'Type-safe implementation patterns for services, APIs, and shared utilities using static typing.'
---

# Static Typing Practices

## When to Use
- Writing services, route handlers, middleware, and shared helpers.
- Defining request/response types and domain entities.

## Procedure
1. Define domain interfaces and union types before implementation.
2. Prefer discriminated unions for success/failure results.
3. Use constrained generics for reusable utilities.
4. Avoid unsafe type escapes; use explicit types or safe unknown handling with narrowing.

## Patterns (pseudocode)

### Discriminated Union Result Type
```
type Result<T> =
  | { ok: true;  value: T }
  | { ok: false; error: string }
```

### Domain Entity
```
type UserRole = 'admin' | 'manager' | 'viewer'

interface User {
  id:    string
  email: string
  role:  UserRole
}
```

### Type Guard
```
function isAppError(err: unknown): err is AppError {
  return err instanceof AppError
}
```

### Generic with Constraint
```
function getById<T extends { id: string }>(items: T[], id: string): T | undefined {
  return items.find(item => item.id === id)
}
```

<details>
<summary>TypeScript implementation reference</summary>

```ts
type Result<T> =
  | { ok: true; value: T }
  | { ok: false; error: string };

interface User {
  id: string;
  email: string;
  role: 'admin' | 'manager' | 'viewer';
}

// Narrowing with type guards
function isAppError(err: unknown): err is AppError {
  return err instanceof AppError;
}

// Generic with constraint
function getById<T extends { id: string }>(items: T[], id: string): T | undefined {
  return items.find(item => item.id === id);
}
```
</details>

## Unknown vs Any
- Use `unknown` when type is genuinely unknown — forces explicit narrowing before use.
- Never use `any` (or equivalent unsafe type escape) — it silently disables all type checking.
- Use type assertions only when the type system cannot infer but you are certain — document why.

## Checklist
- [ ] No unsafe type escapes (`any`, unconstrained casts) in production code.
- [ ] Union types preferred over free-text string fields.
- [ ] Discriminated unions used for branching on variant data shapes.
- [ ] Type errors caught at compile time — static type check passes with `--strict` or equivalent.
- [ ] Public interfaces documented with comments where intent is not obvious from types.
- [ ] External inputs handled as unknown type, narrowed before first use.

## Anti-Patterns
- Unsafe cast to silence a type error — hides real bugs.
- `// @ts-ignore` or equivalent without an explanation comment — creates invisible technical debt.
- `interface Foo { data: any }` — defeats the purpose of the interface.
- Non-discriminated union: `type Status = string` — use `'active' | 'inactive' | 'pending'`.
