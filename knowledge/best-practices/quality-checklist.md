# Comprehensive Quality Checklist

Master checklist for building and reviewing Integra dApps. Used by the `/build`, `/review`, and `/deploy` skills. Applies to both new projects and audits of existing ones.

---

## 1. Smart Contract Quality

### Security
- [ ] ReentrancyGuard on all state-changing external functions
- [ ] Checks-Effects-Interactions pattern (state changes before external calls)
- [ ] Access control on admin functions (Ownable, role-based, or timelocked)
- [ ] Input validation on all public/external functions (zero address, zero amount, bounds)
- [ ] Custom errors instead of require strings (gas efficient + informative)
- [ ] No `tx.origin` for authentication (use `msg.sender`)
- [ ] No `selfdestruct` or `delegatecall` to untrusted addresses
- [ ] SafeERC20 for token transfers (handles non-standard return values)
- [ ] No hardcoded private keys or secrets

### Code Quality
- [ ] Solidity compiler 0.8.24+ (locked pragma, not floating)
- [ ] NatSpec documentation on all public/external functions
- [ ] Events emitted for every state change
- [ ] Constants for magic numbers (`uint256 public constant MAX_SUPPLY = 1_000_000`)
- [ ] Function ordering: constructor → external → public → internal → private
- [ ] PascalCase contracts, camelCase functions, UPPER_SNAKE constants

### Testing
- [ ] Test coverage >90% (run `npx hardhat coverage`)
- [ ] Happy path tests for every public function
- [ ] Failure case tests (unauthorized, invalid input, edge cases)
- [ ] Integration tests for multi-contract interactions
- [ ] Gas benchmarks for critical functions

### Deployment
- [ ] Ignition deployment module (not raw deploy scripts)
- [ ] Deployment configured for correct network (testnet/mainnet from `knowledge/networks/`)
- [ ] Contract verification on explorer post-deploy
- [ ] `.env.example` with all required variables

---

## 2. Frontend Quality

### TypeScript
- [ ] Strict mode enabled (`"strict": true` in tsconfig.json)
- [ ] Zero `any` types in codebase
- [ ] No unsafe casts (`as Type`) — use type guards
- [ ] No non-null assertions (`!`) — validate values
- [ ] Explicit return types on exported functions
- [ ] Props interfaces for all components

### React Patterns
- [ ] Server Components for data fetching (minimize `"use client"`)
- [ ] Suspense boundaries for async components
- [ ] Error Boundaries for component-level failure recovery
- [ ] No unnecessary re-renders (check with React DevTools Profiler)
- [ ] Proper dependency arrays in useEffect/useMemo/useCallback
- [ ] No `console.log` in production code

### UI/UX
- [ ] Loading states: skeleton loaders for content, spinners for actions
- [ ] Error states: user-friendly message + suggested action + retry button
- [ ] Empty states: illustration + explanation + CTA
- [ ] Transaction UX: pending → submitted (with explorer link) → confirmed/failed
- [ ] Toast notifications for success/error
- [ ] Responsive: 375px mobile, 640px tablet, 1024px+ desktop
- [ ] Dark theme is default, all surfaces properly layered
- [ ] Animations are subtle and purposeful (150-300ms, `prefers-reduced-motion` respected)

### Accessibility (WCAG AA)
- [ ] Color contrast 4.5:1 for body text, 3:1 for large text
- [ ] All interactive elements keyboard-accessible
- [ ] Visible focus indicators (`focus-visible`)
- [ ] Semantic HTML (`nav`, `main`, `section`, `button` not `div onClick`)
- [ ] All images have `alt` text (decorative: `alt=""`)
- [ ] ARIA labels on icon-only buttons
- [ ] Form inputs have associated labels
- [ ] `aria-live` regions for dynamic content updates

### Performance
- [ ] Initial bundle < 200KB gzipped
- [ ] `next/image` for all images with explicit dimensions
- [ ] Lazy-load below-fold content
- [ ] LCP < 2.5s, CLS < 0.1

---

## 3. Integra Ecosystem Compliance

### Required Integrations
- [ ] Web3Auth social login (correct network: sapphire_mainnet/sapphire_devnet)
- [ ] Chain ID matches target network (26217 mainnet, 26218 testnet)
- [ ] RPC URLs from `knowledge/networks/` (not hardcoded)
- [ ] XP events emit for all key user actions
- [ ] XP point values are reasonable (no inflation — see conventions.md)

### Token Integration
- [ ] IRL (native) used for gas and primary value
- [ ] tUSDI supported as secondary token (see `knowledge/networks/tokens.md`)
- [ ] WIRL used when contracts need IRL as ERC-20
- [ ] Token addresses from config, not hardcoded
- [ ] Token decimals handled correctly (18 for EVM)
- [ ] Token balances displayed in header/sidebar
- [ ] Token selection guide matches dApp category

### Network Awareness
- [ ] Testnet/mainnet badge visible in UI
- [ ] Wrong-network detection with auto-switch prompt
- [ ] Explorer links use correct domain (from `knowledge/networks/`)
- [ ] Faucet link for testnet apps

### Design System
- [ ] Colors match selected branding (Integra official or custom)
- [ ] Typography uses correct font stack (Euclid Circular B for Integra, custom for custom)
- [ ] No forbidden colors (see `knowledge/design-systems/integra-brand.md`)
- [ ] No forbidden fonts
- [ ] Design matches category adaptation (see `knowledge/best-practices/design-adaptation.md`)
- [ ] Logo renders correctly at all sizes

---

## 4. Documentation

- [ ] README.md with: project description, setup instructions, environment variables, deployment guide
- [ ] docs/PRD.md — personalized product requirements
- [ ] docs/ARCHITECTURE.md — system design
- [ ] docs/CONTRACTS.md — contract interfaces and interactions
- [ ] docs/FRONTEND.md — page specs and component tree
- [ ] docs/INTEGRATIONS.md — ecosystem integration details
- [ ] .env.example with all variables documented
- [ ] NatSpec on all Solidity public functions
- [ ] JSDoc on complex TypeScript functions

---

## 5. Security

- [ ] No secrets in client-side code
- [ ] No `dangerouslySetInnerHTML` usage
- [ ] Input validation before contract calls
- [ ] Transaction confirmation dialogs
- [ ] Rate-limiting on user actions (prevent double-submit)
- [ ] `npm audit` passes with no high/critical vulnerabilities
- [ ] CSP headers configured
- [ ] `.env` in `.gitignore`

---

## 6. Deployment Readiness

- [ ] All tests pass
- [ ] Build succeeds (`npm run build`)
- [ ] No TypeScript errors (`npx tsc --noEmit`)
- [ ] Contract compiles (`npx hardhat compile`)
- [ ] Environment variables documented
- [ ] Deployment scripts tested on testnet
- [ ] Subdomain configured for `appname.integralayer.com`
- [ ] Contract verified on explorer

---

## Usage

### For new projects (`/build`)
Walk through this checklist at Phase 6 (Testing and Review). Every item should be checked.

### For existing projects (`/review`)
Use this as the baseline. Report items that pass and fail. Prioritize failures by severity.

### For audits
Score the project: count passing items / total items. Grade:
- **A**: 90%+ passing
- **B**: 75-89% passing
- **C**: 60-74% passing
- **D**: Below 60% — significant work needed

---

## References

- `knowledge/conventions.md` — Coding standards
- `knowledge/best-practices/ui-ux-design.md` — UI/UX guidelines
- `knowledge/best-practices/accessibility.md` — Accessibility rules
- `knowledge/best-practices/performance.md` — Performance targets
- `knowledge/best-practices/security-frontend.md` — Frontend security
- `knowledge/best-practices/design-adaptation.md` — Category-specific design
- `knowledge/networks/tokens.md` — Token integration requirements
