# UI/UX Design Best Practices

Comprehensive UI/UX guidelines for building premium dApps. These apply to both Integra-branded and custom-branded projects.

---

## 1. Layout Fundamentals

### Visual Hierarchy
- **Primary action** should be the most visually prominent element on every page
- Use size, color, and spacing to create clear information hierarchy: Title → Subtitle → Body → Meta
- Group related elements with consistent spacing; separate groups with larger gaps
- Never place more than 1 primary CTA per viewport

### Grid and Spacing
- Use an 8px base grid for all spacing (4px for micro-adjustments)
- Content max-width: 1280px for dashboards, 720px for reading content
- Card padding: 24px desktop, 16px mobile
- Section spacing: 48-64px between major sections, 24-32px between subsections
- Consistent gutter widths: 16px mobile, 24px tablet, 32px desktop

### Responsive Breakpoints
| Name | Width | Target |
|------|-------|--------|
| Mobile | 375px | Phones |
| Tablet | 640px | Tablets, small laptops |
| Desktop | 1024px | Laptops, desktops |
| Wide | 1440px | Large monitors |

- **Mobile-first approach**: Design for mobile, then enhance for larger screens
- Touch targets: minimum 44x44px on mobile
- No horizontal scroll on any viewport

---

## 2. Component Design

### Cards
- Consistent border-radius across all cards (12px for Integra brand)
- Glassmorphism for elevated surfaces: `backdrop-filter: blur(12px)` + semi-transparent bg
- Hover state: `translateY(-2px)` with subtle shadow increase, 150ms ease
- Cards should have clear visual boundaries (border or shadow, not both)

### Buttons
- Primary: gradient or solid brand color, white text
- Secondary: transparent with border, brand color text
- Ghost: no border, brand color text, hover adds background
- Destructive: danger color background
- All buttons: 6px border-radius, 500 font-weight, 14-16px font-size
- Loading state: replace text with spinner, maintain button width
- Disabled: 50% opacity, `cursor: not-allowed`

### Forms
- Labels above inputs (not placeholder-only)
- Input height: 40-44px for comfortable touch targets
- Validation: show errors below the field in danger color, only after blur or submit
- Success state: subtle green checkmark, not full-field color change
- Never use `placeholder` as the only label

### Tables and Data
- Alternating row colors for readability (subtle, not high-contrast)
- Sticky headers on scroll
- Sortable columns with clear sort indicators
- Empty states: illustration + helpful text + action button
- Loading: skeleton loaders that match the data shape

### Modals and Dialogs
- Max width: 480px for confirmations, 640px for forms
- Always have a clear close mechanism (X button + Escape key + click outside)
- Focus trap within modal
- Animate in: fade + scale from 0.95 to 1, 200ms ease

### Navigation
- Sidebar for dashboards (collapsible on mobile)
- Top nav for landing pages
- Active state: highlighted with brand color
- Breadcrumbs for deep navigation (3+ levels)
- Mobile: bottom tab bar for primary navigation (max 5 items)

---

## 3. States and Feedback

### Loading States
- **Skeleton loaders** for content areas (match the shape of what will load)
- **Spinners** for actions (button submissions, transactions)
- **Progress bars** for multi-step processes
- Never show a blank page — always show loading indication within 100ms

### Error States
- **Inline errors**: below the field, danger color, specific message
- **Page errors**: centered illustration + message + retry button
- **Toast notifications**: top-right, auto-dismiss after 5s (errors stay until dismissed)
- **Network errors**: persistent banner with retry action
- Always tell the user what went wrong AND what they can do about it

### Empty States
- Illustration or icon (not just text)
- Explain what would appear here
- Provide a CTA to create/add the first item
- Use muted colors, don't make empty states feel like errors

### Success States
- Brief toast notification (3s auto-dismiss)
- Transaction confirmations: show tx hash with explorer link
- Subtle animation (checkmark, confetti for milestones — sparingly)

---

## 4. Web3-Specific UX

### Wallet Connection
- Use Web3Auth social login as primary (Google, X, Email)
- Show clear connection status in header
- Handle "wrong network" gracefully: auto-prompt to switch
- Never show raw wallet addresses without truncation: `0x1234...5678`

### Transaction UX
1. **Pre-transaction**: Show what will happen, estimated gas, token amounts
2. **Pending**: Show spinner + "Waiting for confirmation..." 
3. **Submitted**: Show tx hash link to explorer
4. **Confirmed**: Success toast with summary
5. **Failed**: Clear error message + suggestion (insufficient balance, gas too low, etc.)
- Always show a "View on Explorer" link for submitted transactions

### Token Displays
- Show token icon + symbol + amount (e.g., `🔸 1,234.56 IRL`)
- Use locale-aware number formatting (commas for thousands)
- Show USD equivalent when available
- For very small amounts: show up to 6 significant digits
- For very large amounts: abbreviate (1.2M, 3.4B)

### Network Awareness
- Show current network in header/footer
- Color-code testnet vs mainnet (e.g., yellow badge for testnet)
- Warn users before mainnet transactions involving real tokens
- Auto-detect wrong network and offer switch

---

## 5. Accessibility (WCAG AA)

### Color Contrast
- Text on background: minimum 4.5:1 ratio
- Large text (18px+ bold, 24px+ regular): minimum 3:1 ratio
- Interactive elements: minimum 3:1 against adjacent colors
- Never rely on color alone to convey information (add icons/text)

### Keyboard Navigation
- All interactive elements focusable via Tab
- Visible focus indicators (2px solid outline in brand color)
- Escape closes modals/dropdowns
- Enter/Space activates buttons
- Arrow keys navigate within menus/tabs

### Screen Readers
- All images have `alt` text (decorative images: `alt=""`)
- Form inputs have associated `<label>` elements
- Use semantic HTML: `<nav>`, `<main>`, `<section>`, `<article>`
- ARIA labels on icon-only buttons
- Announce dynamic content changes with `aria-live` regions

### Motion
- Respect `prefers-reduced-motion`: disable animations when set
- No auto-playing videos or animations without user consent
- Keep animations under 300ms for UI interactions

---

## 6. Performance UX

### Perceived Performance
- Show content progressively (skeleton → real data)
- Optimistic updates for user actions (show success, revert on failure)
- Prefetch likely next pages on hover
- Lazy-load below-fold images and heavy components

### Image Optimization
- Use Next.js `<Image>` with `loading="lazy"` for below-fold
- WebP format with JPEG fallback
- Provide width/height to prevent layout shift
- Use blur placeholder for large hero images

---

## References

- `knowledge/design-systems/integra-brand.md` — Integra official design tokens
- `knowledge/design-systems/custom-brand.md` — Custom design system template
- `knowledge/best-practices/accessibility.md` — Detailed accessibility guide
- `knowledge/best-practices/performance.md` — Performance optimization guide
