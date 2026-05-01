---
name: api-design
description: 'REST API contract design patterns: versioning, consistent payloads, and predictable error responses.'
---

# API Design

## When to Use
- Creating or changing endpoints.
- Defining request/response contracts and pagination.

## Procedure
1. Version all routes (`/api/v1/...`).
2. Define request, success, and error shapes up front.
3. Standardize pagination and filter query parameters.
4. Use explicit status codes and stable error codes.

## Patterns

### Error Response Shape (pseudocode)
```
ErrorResponse {
  error: string        // Human-readable message
  code: string         // Stable machine-readable code (e.g. AUTH_TOKEN_EXPIRED)
  details?: map        // Optional field-level validation details
}
```

### Pagination Envelope (pseudocode)
```
PaginatedResponse<T> {
  items: T[]
  total: number
  page: number
  pageSize: number
  hasMore: boolean
}
```

<details>
<summary>TypeScript implementation reference</summary>

```ts
interface ErrorResponse {
  error: string;
  code: string;
  details?: Record<string, string>;
}

// Pagination envelope
interface PaginatedResponse<T> {
  items: T[];
  total: number;
  page: number;
  pageSize: number;
  hasMore: boolean;
}
```
</details>

## Status Code Guide
| Situation | Status Code |
|---|---|
| Success | 200 OK |
| Created resource | 201 Created |
| Not found / not owned (private) | 404 Not Found |
| Unauthorized | 401 Unauthorized |
| Forbidden (publicly visible ownership) | 403 Forbidden |
| Validation failure | 422 Unprocessable Entity |
| Server error | 500 Internal Server Error |

## Checklist
- [ ] Endpoint is versioned (`/api/v1/...`).
- [ ] Error response schema is consistent with the ErrorResponse shape.
- [ ] Query params validated and documented.
- [ ] Pagination uses standard envelope for list endpoints.
- [ ] Status codes match the situation table above.
- [ ] Contract covered by integration tests.

> **Note on 403 vs 404:** Use `403 Forbidden` only when the existence of the
> resource is publicly visible (and the user simply lacks access). For
> private resources where ownership is not publicly visible, return
> `404 Not Found` for both "does not exist" and "exists but not owned" to
> prevent enumeration. See `security.md`.

## Anti-Patterns
- Returning 200 with `{ success: false }` body — use appropriate 4xx/5xx status.
- Different error shapes across endpoints — makes client-side handling inconsistent.
- Unversioned routes — breaks clients when the contract changes.
