---
name: api-deprecation
description: 'API deprecation procedure including deprecation notices, sunset headers, migration guides, usage tracking, and breaking change policy.'
---

# API Deprecation

## When to Use
- Removing any public-facing endpoint.
- Changing an endpoint's request or response shape in a backward-incompatible way (breaking change).
- Removing or renaming fields from a response envelope.
- Changing a field's type, making an optional field required, or reordering required parameters.
- Retiring an API version.

## Procedure

### 1. Announce the Deprecation
1. Add a `DEPRECATED` notice to the endpoint's documentation on the day deprecation starts.
2. Add deprecation response headers to every response from the deprecated endpoint (see Deprecation Notice Format).
3. Add a log warning every time the deprecated endpoint is called — include the caller's client identifier if available.
4. Publish a migration guide before announcing the sunset date (see Migration Guide Requirements).
5. Notify known API consumers via your notification channel (email, developer portal, changelog).
6. Record the deprecation start date in `CHANGELOG.md`.

### 2. Set and Communicate the Sunset Date
1. Calculate the sunset date: today + maximum(2 MINOR versions, 90 days). See Breaking Change Policy.
2. Set the `Sunset` response header to the sunset date.
3. Publish the sunset date in the migration guide and developer portal.
4. Add a calendar reminder to enforce the sunset date — do not let it pass silently.

### 3. Track Usage During the Deprecation Window
1. Monitor call volume to the deprecated endpoint using your observability stack.
2. Track unique callers — if a caller goes silent (stops calling), they may have migrated or abandoned.
3. Alert when call volume drops below 5% of peak (likely migration is nearly complete).
4. Alert when the sunset date is 30 days away and call volume is still non-zero — reach out to remaining callers.

### 4. Maintain Backward Compatibility During the Window
1. The deprecated endpoint must continue to work correctly until the sunset date.
2. Do not introduce new bugs or behavior changes to a deprecated endpoint — it must be stable.
3. Security fixes to deprecated endpoints are still required — deprecation does not exempt an endpoint from security maintenance.

### 5. Enforce the Sunset Date
1. On the sunset date, verify call volume is at or near zero. If significant traffic remains, extend the window by 30 days and re-notify active callers.
2. Remove the endpoint from routing.
3. Update the endpoint to return `410 Gone` for at least 30 days after removal — do not return `404` (which implies the endpoint never existed).
4. Update documentation to show the endpoint as removed.
5. Record removal date in `CHANGELOG.md`.

---

## Deprecation Notice Format

### Response Headers
Add these headers to every response from a deprecated endpoint:

```http
Deprecation: true
Sunset: Sat, 01 Jan 2026 00:00:00 GMT
Link: <https://docs.example.com/migration/v2>; rel="successor-version"
```

- `Deprecation: true` — signals to API clients that this endpoint is deprecated.
- `Sunset` — the RFC 7231 date after which the endpoint will stop responding.
- `Link` with `rel="successor-version"` — direct link to the migration guide or replacement endpoint.

### Log Warning on Each Call
```
WARN Deprecated endpoint called: POST /api/v1/users — sunset: 2026-01-01 — caller: {client_id}
```

Log at WARN level. Include: endpoint path, sunset date, and caller identifier. Do not log request body.

### API Version in Response Envelope (Optional but Recommended)
```json
{
  "apiVersion": "1",
  "deprecated": true,
  "sunsetDate": "2026-01-01",
  "migrationGuide": "https://docs.example.com/migration/v2",
  "data": { ... }
}
```

---

## Migration Guide Requirements

Every deprecated endpoint must have a published migration guide before the sunset date is announced. The guide must include:

| Section | Required Content |
|---------|-----------------|
| What's changing | Clear description of what is being removed or changed |
| Why | Reason for the change (not just "deprecated") |
| Before/After examples | Exact request and response examples for old and new API |
| Timeline | Deprecation start date, sunset date |
| Contact | Who to reach for migration help |

### Before/After Example Format

**Before (deprecated):**
```http
POST /api/v1/users
Content-Type: application/json

{ "name": "Alice", "email": "alice@example.com" }
```

**After (replacement):**
```http
POST /api/v2/users
Content-Type: application/json

{ "displayName": "Alice", "emailAddress": "alice@example.com" }
```

---

## Breaking Change Policy

### Definition of a Breaking Change
The following are breaking changes requiring a deprecation window:
- Removing an endpoint.
- Removing a response field that was previously present.
- Changing a field's type (e.g., `string` → `integer`).
- Adding a new required request parameter.
- Changing the semantics of an existing field without changing its name.
- Removing an enum value from a field that clients may have stored.

The following are **NOT** breaking changes (no deprecation window required):
- Adding a new optional response field (clients should ignore unknown fields).
- Adding a new optional request parameter with a safe default.
- Adding a new endpoint.
- Improving error message clarity without changing error codes.

### Minimum Deprecation Window
- **Standard:** maximum(2 MINOR versions, 90 days), whichever is longer.
- **High-traffic endpoints:** maximum(3 MINOR versions, 180 days).
- **Exception — Security fixes:** A breaking change required to fix a security vulnerability may be applied with shorter notice. Minimum: 7 days notice with immediate security advisory. Zero-day security patches may break immediately — post a migration guide within 24 hours.

---

## Checklist

- [ ] Deprecation announced in documentation on deprecation start date.
- [ ] `Deprecation: true`, `Sunset`, and `Link` headers added to all responses from deprecated endpoint.
- [ ] Log warning added for every call to deprecated endpoint.
- [ ] Migration guide published with before/after examples.
- [ ] Sunset date calculated per Breaking Change Policy and communicated.
- [ ] Usage tracking active — monitoring call volume and unique callers.
- [ ] Active callers notified via developer portal or direct communication.
- [ ] Sunset enforced on the sunset date (or extended with re-notification if traffic remained).
- [ ] Endpoint returns `410 Gone` for 30 days after removal.
- [ ] Deprecation and removal recorded in `CHANGELOG.md`.

---

## Anti-Patterns

- **Removing an endpoint without a deprecation period.** Even for low-traffic endpoints, removal without notice breaks integrations silently. Always follow the minimum deprecation window — no exceptions except documented security emergencies.
- **Changing response shape without versioning.** Silently modifying a response field's name or type breaks consumers who did not opt into the change. Shape changes are breaking changes; they require a new API version and a deprecation period for the old shape.
- **Not tracking deprecated endpoint usage.** If you don't know who is still calling the old endpoint, you can't safely remove it. Usage tracking is required — not optional — for every deprecated endpoint.
- **Setting the sunset date in the past.** A sunset date that has already passed when the deprecation is announced gives consumers zero time to migrate. The sunset date must always be in the future and must meet the minimum window requirement.
- **Returning `404` after removal.** `404 Not Found` implies the resource never existed. Use `410 Gone` to signal that the endpoint existed and was intentionally removed, giving client developers a useful error to debug against.
- **Neglecting security maintenance on deprecated endpoints.** A deprecated endpoint is still serving production traffic. Security vulnerabilities must still be patched. Deprecation does not create a security maintenance exemption.
