---
name: accessibility-testing
description: 'Automated and manual accessibility testing patterns using axe-core, keyboard navigation, and WCAG 2.1 AA verification.'
---

# Accessibility Testing

## When to Use
- Adding or modifying user-facing components.
- Before release signoff (accessibility regression check).
- When the Accessibility or UX Designer agent identifies barriers.
- When adding interactive elements (forms, modals, menus, dynamic content).

## Procedure
1. Run automated `{{A11Y_TESTING_LIB}}` scan on affected pages/components.
2. Perform keyboard-only navigation through the modified flow.
3. Verify focus management (focus moves logically, returns after modal close).
4. Check color contrast for new/modified text and UI elements.
5. Test with screen reader for dynamic content changes.
6. Document findings with WCAG criterion reference.

## Automated Testing Integration
```
// Import {{A11Y_TESTING_LIB}} in your E2E test files
// Example: replace import path with the correct one for your setup

test('page has no accessibility violations', async ({ page }) => {
  await page.goto('/dashboard');
  const results = await new AxeBuilder({ page })
    .withTags(['wcag2a', 'wcag2aa'])
    .analyze();
  expect(results.violations).toEqual([]);
});
```

## Keyboard Navigation Checklist
- [ ] All interactive elements reachable via Tab/Shift+Tab.
- [ ] Tab order matches visual reading order.
- [ ] Custom components respond to Enter/Space for activation.
- [ ] Escape closes modals/dropdowns and returns focus to trigger.
- [ ] Arrow keys navigate within composite widgets (tabs, menus, listboxes).
- [ ] Skip-to-content link available on page-level navigation.

## WCAG 2.1 AA Quick Reference
| Criterion | Requirement | How to Test |
|-----------|-------------|-------------|
| 1.1.1 | Non-text content has text alternatives | All `<img>` have `alt`, icons have `aria-label` |
| 1.3.1 | Info/structure conveyed programmatically | Semantic HTML, heading hierarchy, form labels |
| 1.4.3 | Contrast ratio 4.5:1 (normal text) | DevTools accessibility panel or `{{A11Y_TESTING_LIB}}` |
| 2.1.1 | All functionality keyboard-accessible | Tab through entire page |
| 2.4.3 | Focus order is meaningful | Verify tab sequence matches layout |
| 2.4.7 | Focus is visible | Check focus ring on all interactive elements |
| 4.1.2 | UI components have name, role, value | Inspect with accessibility tree |

## Focus Management Patterns (pseudocode)
```
// Store reference to trigger element
triggerRef = ref(null)

function handleClose():
  setIsOpen(false)
  // Return focus to element that opened the modal/dialog
  triggerRef.current.focus()
```

## Anti-Patterns
- Using `div` with `onClick` instead of `button` — loses keyboard access and semantics.
- Setting `outline: none` without replacement focus indicator — makes focus invisible.
- Using `aria-label` on elements that already have visible text — creates redundancy/confusion.
- Using `role="button"` without `tabindex="0"` and keyboard event handlers.
- Toast notifications without `aria-live="polite"` — invisible to screen readers.
- Color-only error indication without icon or text — fails WCAG 1.4.1 Use of Color.
- Placeholder-only form labels — disappear when user starts typing.
