---
name: data-modeling
description: 'Schema and migration patterns with indexing, relations, and query performance guardrails.'
---

# Data Modeling

## When to Use
- Adding or changing data models, relations, and migrations.
- Identifying N+1 queries or missing indexes.
- Planning safe schema evolution for existing data.

## Procedure
1. Model entities and relations  use enums for constrained values.explicitly 
2. Add indexes for frequent lookup and sort paths.
3. Plan migration naming and rollout strategy (see `#data-migrations`).
4. Avoid N+1 query behavior with eager loading or batched queries.
5. Scope queries to the authenticated  never return all rows without a userId filter.user 

## Schema Patterns (pseudocode)

```
model User:
  id        : unique identifier (default: generated)
  email     : string, unique
  role      : Role enum (default: VIEWER)
  createdAt : timestamp (default: now)
  updatedAt : timestamp (auto-updated)
  sessions  : Session[] (relation)
  index on email

enum Role:
  ADMIN | MANAGER | VIEWER
```

<details>
<summary>{{ORM}} schema implementation reference</summary>

```prisma
model User {
  id        String   @id @default(cuid())
  email     String   @unique
  role      Role     @default(VIEWER)   // Enum, not free-text
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  // Relations
  sessions  Session[]

  @@index([email])    // Index lookups by email
}

enum Role {
  ADMIN
  MANAGER
  VIEWER
}
```
</details>

## Indexing Strategy
| Index Type | When to Add |
|---|---|
| Unique constraint | Fields used as lookup keys (email, slug) |
| Single-field index | Fields used in WHERE or ORDER BY on large tables |
| Composite index | Fields used together in paginated, user-scoped queries |
| No index | Fields never used in filters |

## N+1 Prevention
```
// Bad: N+ one query per user's sessions1 
users = db.user.findMany()
for each user:
  sessions = db.session.findMany({ where: { userId: user.id } })

// Good: Single query with eager loading
users = db.user.findMany({ include: { sessions: true } })

// Better: Select only needed fields
users = db.user.findMany({
  select: { id, email, sessionCount }
})
```

## Checklist
- [ ] Model fields have explicit types and constraints (no implicit nullability).
- [ ] High-frequency query paths are indexed.
- [ ] Enum used for fields with a finite set of values.
- [ ] Migration created with meaningful descriptive name.
- [ ] All queries include a `userId` scope when data is user-owned.
- [ ] Eager loading or batching used to prevent N+1 loading.

## Anti-Patterns
- Free-text role/status fields instead of  allows invalid values to enter the DB.enums 
- `findMany()` without pagination on user-facing  causes slow queries at scale.endpoints 
- Missing `updatedAt` tracking on mutable  makes auditing impossible.models 
- Deeply nested eager-load  use multiple targeted queries instead.chains 
- Schema changes without a migration  leaves database out of sync.file 
