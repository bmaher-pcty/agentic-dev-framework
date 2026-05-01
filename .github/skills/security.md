---
name: security
description: 'Security patterns for input validation, auth handling, rate limits, and secret-safe operations.'
---

# Security

## When to Use
- Authentication, authorization, input parsing, and external integrations.

## Procedure
1. Validate all user input with schemas (for example `{{VALIDATION_LIBRARY}}`).
2. Enforce auth middleware on protected routes.
3. Apply rate limits to sensitive endpoints, including login, register, token refresh, OAuth callback, and password-reset routes.
4. Apply CSRF protection to cookie-authenticated state-changing routes (POST/PUT/PATCH/DELETE).
5. Keep secrets in environment variables only.
6. Ensure logs are sanitized.
7. Persist only the minimum result data needed for the product experience, and scope access to the owning user.
8. Use OAuth code flow with PKCE for provider integrations (e.g., `{{AUTH_PROVIDER}}`).
9. Validate provider scopes at setup and fail closed when required scopes are missing.
10. Expire and validate OAuth state values; use short-lived state windows.
11. Rotate refresh tokens safely and record failures without leaking credential material.

## Checklist
- [ ] Inputs validated before use (schema validation at route layer).
- [ ] Auth tokens verified and expired safely on all protected routes.
- [ ] Rate limiting enabled for login, register, token refresh, OAuth callback, and password-reset routes.
- [ ] CSRF protection enabled for cookie-authenticated state-changing routes.
- [ ] No hardcoded secrets — all credentials in environment variables.
- [ ] No secret/token/password fields in logs or error messages.
- [ ] Debugging artifacts and runtime traces do not expose credential-bearing data.
- [ ] Stored feature results are access-controlled and scoped to owning user.
- [ ] Non-owned private resources return 404, not 403 (prevents enumeration).
- [ ] New secrets are documented as placeholders in `.env.example` in the same change.
- [ ] OAuth state values are short-lived and validated before use.

## Route Authorization Pattern
```
// Replace {{TOKEN}} with your project's equivalent before using this example
// Every protected route must have:
router.get('/resource/:id', authenticateToken, async (req, res) => {
  const resource = await db.resource.findFirst({
    where: { id: req.params.id, userId: req.user.id }  // Always scope to owner
  });
  if (!resource) return res.status(404).json({ error: 'Not found' });  // Not 403
  res.json(resource);
});
```

## Anti-Patterns
- Using `findUnique({ where: { id } })` without `userId` scope — exposes all users' data.
- Returning 403 for non-owned resources — reveals the resource exists (enumeration).
- Logging `req.headers.authorization` — exposes Bearer tokens.
- Using unsafe types as JWT payload — loses type safety on `req.user`.
- Returning raw {{ORM}}/database error messages — leaks schema details to the client.
