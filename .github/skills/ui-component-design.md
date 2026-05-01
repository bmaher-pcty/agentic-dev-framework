---
name: ui-component-design
description: 'UI component design patterns for small APIs, composition, and accessible defaults.'
---

# UI Component Design

## When to Use
- Building or refactoring UI components.
- Deciding between a new component and extending an existing one.
- Designing prop APIs for shared/reusable components.

## Procedure
1. Keep component responsibilities narrow (single purpose per component).
2. Define strict prop interfaces with explicit defaults.
3. Prefer composition over oversized prop-driven components.
4. Include all visual states: default, hover, focus, disabled, loading, error, empty.
5. Use semantic HTML elements before reaching for ARIA attributes.
6. Expose a `className` or style extension mechanism for customization without forking.

## Component Categories
| Type | Rule |
|---|---|
| **Page** | Thin orchestrator — fetches data, composes sections. ≤80 lines. |
| **Feature** | Owns a user-facing workflow. Connects to stores/queries. |
| **UI** | Pure presentation — no data fetching, no store access. Reusable. |
| **Layout** | Structure only (grid, flex, spacing). No business logic. |

## Prop API Pattern (pseudocode)
```
ButtonProps {
  children    : node/content
  variant?    : 'primary' | 'secondary' | 'danger'
  size?       : 'sm' | 'md' | 'lg'
  isLoading?  : boolean
  disabled?   : boolean
  onClick?    : function
  className?  : string    // Always expose for extension
  aria-label? : string    // Required when no visible label
}
```

<details>
<summary>{{FRONTEND_FRAMEWORK}} + TypeScript implementation reference</summary>

```typescript
interface ButtonProps {
  children: React.ReactNode;
  variant?: 'primary' | 'secondary' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  isLoading?: boolean;
  disabled?: boolean;
  onClick?: () => void;
  className?: string;        // Always expose for extension
  'aria-label'?: string;     // Required when no visible label
}
```
</details>

## Visual State Checklist
Every interactive component must define:
- [ ] **Default** — resting state.
- [ ] **Hover** — cursor affordance.
- [ ] **Focus** — keyboard visibility (use `{{CSS_FRAMEWORK}}` focus-visible utilities or equivalent).
- [ ] **Disabled** — opacity + cursor change, `disabled` or `aria-disabled`.
- [ ] **Loading** — spinner or skeleton, prevent double-submit.
- [ ] **Error** — clear message, associated with field via `aria-describedby`.
- [ ] **Empty** — meaningful empty state, not a blank void.

## Composition Example (pseudocode)
```
// Prefer: composable
<Card>
  <Card.Header>Title</Card.Header>
  <Card.Body>Content</Card.Body>
</Card>

// Avoid: monolithic prop-driven
<Card title="Title" content="Content" footer="..." headerAction={<Button />} />
```

<details>
<summary>{{FRONTEND_FRAMEWORK}} composition implementation reference</summary>

```tsx
// Prefer: composable
<Card>
  <Card.Header>Title</Card.Header>
  <Card.Body>Content</Card.Body>
</Card>

// Avoid: monolithic prop-driven
<Card title="Title" content="Content" footer="..." headerAction={<Button />} />
```
</details>

## Checklist
- [ ] Props are typed, minimal, and have sensible defaults.
- [ ] Component exposes style extension mechanism.
- [ ] All visual states are handled.
- [ ] Markup uses semantic elements (`button`, `nav`, `main`, `article`).
- [ ] No data fetching in UI components — pass data as props.

## Anti-Patterns
- Components >200 lines — decompose into sub-components.
- Prop drilling >2 levels deep — consider context or co-location.
- Using `div` + `onClick` instead of `button` — loses keyboard and semantics.
- Forgetting the empty state — shows broken/confusing UI when data is absent.
