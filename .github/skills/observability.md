---
name: observability
description: 'Structured logging, health checks, error tracking, and runtime diagnostics patterns for production readiness.'
---

# Observability

## When to Use
- Adding or modifying API endpoints (request/response logging).
- Implementing health check or readiness probe endpoints.
- Debugging production issues via log analysis.
- Adding error tracking or monitoring integration points.
- After infrastructure changes to verify service health.

## Procedure
1. Use structured JSON logging with consistent field names.
2. Include correlation IDs (request ID) in all log entries for a request lifecycle.
3. Log at appropriate levels: error (failures), warn (degraded), info (business events), debug (development).
4. Implement health check endpoint with dependency verification.
5. Never log sensitive data (tokens, passwords, PII).
6. Add timing instrumentation to slow or critical paths.

## Structured Log Format (pseudocode)

```
LogEntry {
  timestamp   : ISO 8601 string
  level       : 'error' | 'warn' | 'info' | 'debug'
  message     : string
  requestId?  : string        // Correlation ID
  userId?     : string        // Auth user ID only — never PII beyond that
  service     : string        // e.g. 'api' | 'worker' | 'scheduler'
  duration_ms?: number        // For timed operations
  error?      : { code, stack? }
  metadata?   : map           // Additional context
}
```

<details>
<summary>TypeScript implementation reference</summary>

```typescript
interface LogEntry {
  timestamp: string;       // ISO 8601
  level: 'error' | 'warn' | 'info' | 'debug';
  message: string;
  requestId?: string;      // Correlation ID
  userId?: string;         // Authenticated user (ID only, never PII beyond that)
  service: string;         // 'api' | 'worker' | 'scheduler'
  duration_ms?: number;    // For timed operations
  error?: {
    code: string;
    stack?: string;        // Only in non-production or when level=error
  };
  metadata?: Record<string, unknown>;  // Additional context
}
```
</details>

## Health Check Pattern
```
// GET /health — basic liveness
app.get('/health', (req, res) => {
  res.json({ status: 'ok', timestamp: now() })
})

// GET /health/ready — readiness with dependency checks
app.get('/health/ready', async (req, res) => {
  checks = { database: await checkDatabase() }
  healthy = all checks status == 'ok'
  res.status(healthy ? 200 : 503).json({
    status: healthy ? 'ok' : 'degraded',
    checks
  })
})
```

## Request ID Pattern
```
// Middleware: attach request ID to every request
app.use((req, res, next) => {
  req.id = req.headers['x-request-id'] || generateUUID()
  next()
})
```

## Checklist
- [ ] All endpoints log request receipt and completion with timing.
- [ ] Error responses are logged with stack trace and request context.
- [ ] Health endpoint verifies actual dependency connectivity.
- [ ] No tokens, passwords, or PII in log output.
- [ ] Request IDs propagate through the full request lifecycle.
- [ ] Log level is configurable via environment variable (`LOG_LEVEL`).
- [ ] Critical errors include enough context for reproduction without the original request.

## Anti-Patterns
- Logging `req.headers.authorization` or `req.body.password` — leaks credentials.
- Health check that returns 200 without verifying dependencies — hides outages.
- Using `console.log` instead of structured logger — loses queryability.
- Logging every request body in production — performance and privacy risk.
- Missing request ID — makes correlating distributed logs impossible.
- Using `error.message` from external APIs without sanitization — may leak internal URLs.
