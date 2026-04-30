---
name: ui-principles
description: 'UI design principles for hierarchy, spacing, responsive layouts, and accessibility-first styling.'
---

# UI Principles

## When to Use
- Creating layouts, design tokens, and visual hierarchy decisions.
- Reviewing components for clarity, hierarchy, and accessible defaults.
- Deciding how to surface success, error, and empty states.

## Procedure
1. Establish clear hierarchy with typography and spacing.
2. Use consistent `{{CSS_FRAMEWORK}}` scale and spacing rhythm.
3. Build mobile-first responsive layouts.
4. Ensure keyboard/focus and color contrast accessibility (WCAG 2.1 AA minimum).
5. Make the outcome of primary actions obvious, nearby, and persistent.

## {{CSS_FRAMEWORK}} Spacing Scale
> Note: values below reflect Tailwind CSS defaults. Adapt to your configured `{{CSS_FRAMEWORK}}` scale.

Use multiples of 4px. Common patterns:
| Context | Class | Size |
|---|---|---|
| Inline padding (button, tag) | `px-3 py-1.5` | 12px / 6px |
| Card padding | `p-4` or `p-6` | 16px or 24px |
| Section gap | `gap-4` or `gap-6` | 16px or 24px |
| Page max width | `max-w-7xl mx-auto px-4` | Standard |

## Typography Hierarchy
```
 Page titles (h1)
 Section headers (h2)
 Body content
 Supporting/metadata text
 Labels, badges, timestamps
```

## Result Visibility Rules
- **Primary action results** must be visible without scrolling after the action completes.
- **Success states** must be persistent (not a 3-second toast) when the user needs to inspect or act on the result.
- **Error states** must name the cause and provide a recovery path.
- **Empty states** must explain why it's empty and what to do  never show a blank area.next 

## Responsive Breakpoints
```
 xl: (1280px)
```
Core rule: define the mobile layout first, then override for larger screens.

## Focus and Contrast Checklist
- [ ] All interactive elements have visible focus rings (`{{CSS_FRAMEWORK}}` focus-visible utilities).
- [ ] Text 4.5:1 for normal 3:1 for large 18px or 14px bold).text (text, contrast: 
- [ ] UI component contrast (buttons, 3:1 against adjacent background.inputs): 
- [ ] Color is never the sole indicator of meaning (add icon or text).

## Checklist
 metadata.
- [ ] Responsive behavior is intentional at all four breakpoints.
- [ ] Interactive states (hover, focus, disabled, loading) are all defined.
- [ ] WCAG contrast and keyboard navigation are respected.
- [ ] Core workflows do not hide results in transient banners when users need an inspectable surface.
- [ ] Empty states explain context and provide a next action.

## Anti-Patterns
- Using `fixed` or `absolute` positioning for content that should  breaks on small screens.flow 
- Toast-only success state for create/save  user can't inspect what was created.actions 
- Placeholder text as the only  disappears on focus, fails accessibility.label 
- Hiding action buttons until  fails keyboard users and touch devices.hover 
