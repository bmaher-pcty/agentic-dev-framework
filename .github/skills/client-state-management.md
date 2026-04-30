---
name: client-state-management
description: 'Client-side state management guidance for separating app state from server state and managing cache invalidation and logout cleanup.'
---

# Client State Management

## When to Use
- Managing auth/session, filters, cached API data, and shared UI state.
- Deciding whether data belongs in local app state vs server-sourced cache.
- Designing cache invalidation or logout cleanup strategies.

## State Separation Rule
| State Type | Store | Examples |
|---|---|---|
| Server-sourced data | Data fetching library (e.g. `{{DATA_FETCHING_LIB}}`) | Issues, users, metrics, projects |
| UI/interaction state | App state store (e.g. `{{STATE_MANAGEMENT_LIB}}`) | Selected filters, sidebar open, modal state |
| Auth/session | App state store (persisted) | Logged-in user, token expiry, permissions |
| Form state | Local component state | Input values, validation errors |

## Procedure
1. Keep app/global UI state in a dedicated state store.
2. Keep server-sourced state in your data fetching  do not copy API responses into app state.library 
3. Define stable, hierarchical query keys for all data fetching.
4. Set appropriate stale/cache times per data  never rely on a default of 0.type 
5. Persist only required state (auth) and clear all session state on logout.
6. Invalidate related server caches after mutations.

## App State Store Pattern (pseudocode)
```
// authStore
AuthState {
  user: User | null
  isAuthenticated: boolean
  login(user: User): void
  logout(): void
}

// Persist auth state across page loads
// Clear all server cache on logout
```

<details>
<summary>{{STATE_MANAGEMENT_LIB}} + {{DATA_FETCHING_LIB}} implementation reference</summary>

```typescript
// stores/authStore.ts
interface AuthState {
  user: User | null;
  isAuthenticated: boolean;
  login: (user: User) => void;
  logout: () => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      isAuthenticated: false,
      login: (user) => set({ user, isAuthenticated: true }),
      logout: () => set({ user: null, isAuthenticated: false }),
    }),
    { name: 'auth-storage', partialize: (state) => ({ user: state.user }) }
  )
);
```
</details>

## Logout Cleanup Pattern (pseudocode)
```
function logout():
  1. Clear auth store state
  2. Clear ALL server data cache
  3. Redirect to login
```

<details>
<summary>{{STATE_MANAGEMENT_LIB}} + {{DATA_FETCHING_LIB}} logout reference</summary>

```typescript
const logout = () => {
  // 1. Clear auth store
  useAuthStore.getState().logout();
  // 2. Clear ALL React Query cache
  queryClient.clear();
  // 3. Redirect to login
  navigate('/login');
};
```
</details>

## Checklist
- [ ] App state and server state are not mixed in the same store.
- [ ] Query keys are deterministic and defined in a central factory.
- [ ] Cache invalidation paths are defined for every mutation.
- [ ] Sensitive state (user, tokens) is cleared on session end.
- [ ] No API response data is duplicated in app state store.
- [ ] Stale/cache times are explicitly set on all queries.

## Anti-Patterns
- Copying data-fetching library data into app  creates stale data and synchronization bugs.state 
- Using app state for server  misses automatic refetching, error handling, caching.state 
- Not clearing data cache on  shows previous user's data to next user.logout 
- Using one giant app state  creates unnecessary re-renders and poor maintainability.store 
