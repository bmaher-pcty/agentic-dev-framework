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
3. Apply rate limits to sensitive endpoints.
4. Keep secrets in environment variables only.
5. Ensure logs are sanitized.
6. Persist only the minimum result data needed for the product experience, and scope access to the owning user.
7. Use OAuth code flow with PKCE for provider integrations.
8. Validate provider scopes at setup and fail closed when required scopes are missing.
9. Expire and validate OAuth state values; use short-lived state windows.
10. Rotate refresh tokens safely and record failures without leaking credential material.

## Checklist
- [ ] Inputs validated before use (schema validation at route layer).
- [ ] Auth tokens verified and expired safely on all protected routes.
- [ ] Rate limiting enabled for login, register, and token refresh routes.
- [ ] No hardcoded  all credentials in environment variables.secrets 
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
- Using `findUnique({ where: { id } })` without `userId`  exposes all users' data.scope 
- Returning 403 for non-owned  reveals the resource exists (enumeration).resources 
- Logging `req.headers. exposes Bearer tokens.authorization` 
- Using unsafe types as JWT  loses type safety on `req.user`.payload 
- Returning raw {{ORM}}/database error  leaks schema details to the client.messages 
