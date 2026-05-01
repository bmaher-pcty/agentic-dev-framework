---
name: async-data-fetching
description: 'Async data fetching patterns for cache management, optimistic updates, error boundaries, and server state synchronization.'
---

# Async Data Fetching

## When to Use
- Fetching, caching, or synchronizing server state in UI components.
- Implementing optimistic updates for user-facing mutations.
- Designing cache invalidation strategies after mutations.
- Handling loading, error, and empty states for async data.

## Procedure
1. Define query keys as constants with hierarchical structure.
2. Set appropriate stale and cache times based on data freshness requirements.
3. Implement error boundaries for query failures.
4. Use mutation lifecycle hooks (`onMutate`/`onError`/`onSettled`) for optimistic updates.
5. Invalidate related queries after successful mutations.
6. Handle loading, error, and empty states in every component that uses async data.

## Query Key Convention (pseudocode)
```
// Central query key factory
queryKeys = {
  users: {
    all:    ['users'],
    lists:  () => [...all, 'list'],
    list:   (filters) => [...lists(), filters],
    detail: (id) => [...all, 'detail', id],
  },
  projects: {
    all:    ['projects'],
    list:   () => [...all, 'list'],
    issues: (projectId) => [...all, 'issues', projectId],
  }
}
```

<details>
<summary>{{DATA_FETCHING_LIB}} implementation reference</summary>

```typescript
// keys.ts — central query key factory
export const queryKeys = {
  users: {
    all: ['users'] as const,
    lists: () => [...queryKeys.users.all, 'list'] as const,
    list: (filters: UserFilters) => [...queryKeys.users.lists(), filters] as const,
    details: () => [...queryKeys.users.all, 'detail'] as const,
    detail: (id: string) => [...queryKeys.users.details(), id] as const,
  },
  jira: {
    all: ['jira'] as const,
    projects: () => [...queryKeys.jira.all, 'projects'] as const,
    issues: (projectId: string) => [...queryKeys.jira.all, 'issues', projectId] as const,
  },
} as const;
```
</details>

## Optimistic Update Pattern (pseudocode)
```
mutation(updateUser):
  onMutate(newData):
    cancel in-flight queries for this item
    save previous value
    set optimistic value in cache
    return { previous }
  onError(err, newData, context):
    restore previous value from context
  onSettled():
    invalidate all related queries
```

<details>
<summary>{{DATA_FETCHING_LIB}} optimistic update reference</summary>

```typescript
const mutation = useMutation({
  mutationFn: updateUser,
  onMutate: async (newData) => {
    await queryClient.cancelQueries({ queryKey: queryKeys.users.detail(newData.id) });
    const previous = queryClient.getQueryData(queryKeys.users.detail(newData.id));
    queryClient.setQueryData(queryKeys.users.detail(newData.id), newData);
    return { previous };
  },
  onError: (_err, newData, context) => {
    queryClient.setQueryData(queryKeys.users.detail(newData.id), context?.previous);
  },
  onSettled: () => {
    queryClient.invalidateQueries({ queryKey: queryKeys.users.all });
  },
});
```
</details>

## Stale Time Guidelines
| Data Type | staleTime | Rationale |
|-----------|-----------|-----------|
| User profile | 5 min | Changes infrequently |
| Dashboard metrics | 30 sec | Should feel current |
| Sync status | 10 sec | Active sync needs freshness |
| Static config | Infinity | Loaded once per session |

## Checklist
- [ ] Query keys are hierarchical and defined in a central factory.
- [ ] Every query hook handles loading, error, and empty states.
- [ ] Mutations invalidate related queries on success.
- [ ] Optimistic updates include rollback on error.
- [ ] Stale time is explicitly set (never rely on default 0).
- [ ] Error boundaries catch and display query failures gracefully.

## Anti-Patterns
- Using string query keys inline — causes typo bugs and makes invalidation unreliable.
- Setting infinite stale time for data that changes — shows stale data indefinitely.
- Forgetting error rollback in optimistic updates — leaves UI in inconsistent state.
- Invalidating queries with overly broad keys — causes unnecessary refetches.
- Not handling the loading state — shows empty/broken UI during fetches.
