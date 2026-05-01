---
name: error-handling
description: 'Structured error handling patterns with typed application errors, middleware mapping, and safe logging.'
---

# Error Handling

## When to Use
- Any code path that can fail (DB, API, auth, validation).

## Procedure
1. Throw typed application errors for expected failures.
2. Map known errors to stable HTTP responses.
3. Log unexpected errors with context and stack.
4. Never leak sensitive values in error messages.

## Patterns

### Application Error Class (pseudocode)
```
class AppError extends Error:
  message: string       // Human-readable message
  code: string          // Stable machine-readable code
  statusCode: number    // HTTP status (default 500)
  details?: map         // Optional additional context

// Route-level error handler middleware:
function handleServiceError(err, req, res, next):
  if err is AppError:
    return res.status(err.statusCode).json({ error: err.message, code: err.code })
  log.error({ err, requestId: req.id }, 'Unhandled error')
  res.status(500).json({ error: 'Internal server error', code: 'INTERNAL_ERROR' })
```

<details>
<summary>TypeScript implementation reference</summary>

```ts
class AppError extends Error {
  constructor(
    public message: string,
    public code: string,
    public statusCode = 500,
    public details?: Record<string, unknown>
  ) {
    super(message);
  }
}

// Route-level error handler middleware
function handleServiceError(err: unknown, req: Request, res: Response, next: NextFunction) {
  if (err instanceof AppError) {
    return res.status(err.statusCode).json({ error: err.message, code: err.code });
  }
  logger.error({ err, requestId: req.id }, 'Unhandled error');
  res.status(500).json({ error: 'Internal server error', code: 'INTERNAL_ERROR' });
}
```
</details>

## Error Code Convention
Format: `DOMAIN_NOUN_VERB` — e.g., `AUTH_TOKEN_EXPIRED`, `PROJECT_NOT_FOUND`, `USER_EMAIL_DUPLICATE`.

## Checklist
- [ ] Known failures use typed application errors with stable code strings.
- [ ] Middleware returns consistent error payloads with `error` and `code` fields.
- [ ] Unexpected failures logged once with context (requestId, userId) and stack trace.
- [ ] No secrets, tokens, or credentials appear in error messages or logs.
- [ ] 4xx errors are not logged as errors — log at `warn` level for client mistakes.

## Anti-Patterns
- Swallowing errors with empty `catch` blocks — failures become invisible.
- Different error shapes on different routes — forces per-route client handling.
- Logging `error.stack` in production for every 4xx — noisy and potentially revealing.
- Returning raw ORM/database error messages — leaks schema details to the client.
