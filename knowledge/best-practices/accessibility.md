# Accessibility Best Practices (WCAG AA)

Every Integra dApp must meet WCAG 2.1 AA standards minimum. Accessibility is not optional — it ensures all users, regardless of ability, can interact with the dApp.

---

## 1. Color and Contrast

- **Body text**: minimum 4.5:1 contrast ratio against background
- **Large text** (18px bold / 24px regular): minimum 3:1 ratio
- **Interactive elements**: minimum 3:1 against adjacent colors
- **Never use color alone** to convey meaning (pair with icons, text, or patterns)
- **Test dark theme contrast**: Dark backgrounds can make low-contrast text invisible
- **Tool**: Use Chrome DevTools Accessibility pane or axe-core for automated checks

### Integra-Specific
- Text (#E6E6E6) on Background (#0A0A0F) = 15.3:1 ratio (excellent)
- Muted text (#A3A3A3) on Background (#0A0A0F) = 8.7:1 ratio (good)
- Subtle text (#6C757D) on Background (#0A0A0F) = 4.8:1 ratio (passes AA for body text)
- Coral (#FF6D49) on Background (#0A0A0F) = 5.2:1 ratio (passes AA)

---

## 2. Keyboard Navigation

- **Tab order**: All interactive elements reachable via Tab in logical order
- **Focus indicators**: Visible 2px solid outline in brand color. Never remove `:focus-visible`
- **Skip links**: "Skip to main content" link as first focusable element
- **Escape**: Closes modals, dropdowns, popovers
- **Enter/Space**: Activates buttons and links
- **Arrow keys**: Navigate within menus, tabs, radio groups, comboboxes
- **Focus trapping**: Modal dialogs trap focus until dismissed

```css
/* Never do this */
*:focus { outline: none; }

/* Do this instead */
*:focus-visible {
  outline: 2px solid var(--integra-brand);
  outline-offset: 2px;
}
```

---

## 3. Semantic HTML

- Use `<nav>` for navigation, `<main>` for primary content, `<aside>` for sidebars
- Use `<section>` with headings, `<article>` for self-contained content
- Use `<button>` for actions, `<a>` for navigation — never use `<div onClick>`
- Heading hierarchy: `h1` → `h2` → `h3` (never skip levels)
- Lists: Use `<ul>/<ol>` for lists, not styled divs
- Tables: Use `<table>` with `<thead>`, `<th scope>` for data tables

---

## 4. ARIA Labels

- **Icon buttons**: `aria-label="Close modal"` (not just an X icon)
- **Loading states**: `aria-busy="true"` on containers while loading
- **Live regions**: `aria-live="polite"` for toast notifications, `aria-live="assertive"` for errors
- **Expanded state**: `aria-expanded="true/false"` on dropdowns/accordions
- **Current page**: `aria-current="page"` on active nav item
- **Form errors**: `aria-describedby` linking input to error message
- **Decorative images**: `alt=""` and `aria-hidden="true"`

---

## 5. Forms and Inputs

- Every input has a visible `<label>` element (not just placeholder)
- Error messages linked via `aria-describedby`
- Required fields marked with `aria-required="true"` and visible indicator
- Group related inputs with `<fieldset>` and `<legend>`
- Auto-complete attributes for common fields (`autocomplete="email"`, etc.)
- Don't auto-advance focus on input (let users control pace)

---

## 6. Motion and Animation

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

- Respect `prefers-reduced-motion` system preference
- No auto-playing animations or videos
- No content that flashes more than 3 times per second
- Provide pause/stop controls for any animation lasting >5 seconds

---

## 7. Web3-Specific Accessibility

- **Wallet addresses**: Provide copy button (don't force users to select text)
- **Transaction status**: Use `aria-live` regions for real-time tx updates
- **Network switching**: Clear, screen-reader-friendly network status
- **Token amounts**: Use `aria-label` with full readable amount ("1,234.56 IRL tokens")
- **Gas estimates**: Present in readable format, not raw wei values

---

## 8. Testing Checklist

- [ ] All pages pass axe-core with zero violations
- [ ] Tab through entire app — all elements reachable and logically ordered
- [ ] Screen reader (VoiceOver/NVDA) can navigate all pages
- [ ] All images have meaningful alt text (or empty alt for decorative)
- [ ] Color contrast passes 4.5:1 for body text
- [ ] Forms are usable without a mouse
- [ ] `prefers-reduced-motion` disables animations
- [ ] No content relies solely on color to convey meaning

---

## References

- [WCAG 2.1 AA Checklist](https://www.w3.org/WAI/WCAG21/quickref/?levels=aa)
- `knowledge/best-practices/ui-ux-design.md` — General UX guidelines
- `knowledge/design-systems/integra-brand.md` — Contrast ratios for Integra colors
