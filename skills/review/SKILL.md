---
name: review
description: Review your dApp code for quality, security, and Integra compatibility.
user_invocable: true
trigger: "integra-studio:review"
---

# Review Skill

Comprehensive code review of an Integra dApp project. Checks security, quality, ecosystem compliance, test coverage, and documentation.

## CRITICAL: Use AskUserQuestion for ALL interactions

**Every question, confirmation, and decision point MUST use the `AskUserQuestion` tool.** Never output a question as plain text. Always use AskUserQuestion so the user gets a proper interactive prompt.

## Activation

When invoked, read the full project to understand scope, then delegate to the **reviewer** agent for analysis.

### Required Reading Before Review
- `knowledge/networks/tokens.md` — Token registry for compliance checks
- `knowledge/best-practices/quality-checklist.md` — Master quality checklist (use as review framework)
- `knowledge/best-practices/design-adaptation.md` — Category-specific design validation
- `knowledge/best-practices/ui-ux-design.md` — UI/UX compliance checks
- `knowledge/best-practices/accessibility.md` — WCAG AA checklist
- `knowledge/best-practices/performance.md` — Performance targets
- `knowledge/best-practices/security-frontend.md` — Frontend security checks
- `knowledge/best-practices/onboarding-ux.md` — User onboarding quality
- `.integra/config.json` — Project configuration (branding, network, category, tokens)

## Review Process

### Step 1: Project Scan

Scan the project structure to understand what exists:
- List all Solidity contracts in `contracts/`
- List all frontend pages in `src/app/`
- List all hooks in `src/hooks/`
- List all tests in `test/`
- Check for docs/ folder (PRD, ARCHITECTURE, etc.)
- Check for deployment files

Show the scan summary before starting the review.

### Step 2: Contract Security Review

For each Solidity contract, check:

**Critical (must fix):**
- Reentrancy vulnerabilities — external calls before state changes, missing `nonReentrant`
- Unchecked return values on external calls (`.transfer()`, `.call()`, ERC-20 `transferFrom`)
- Access control — functions that modify state must have proper modifiers (`onlyOwner`, `onlyRole`, etc.)
- Integer overflow/underflow — Solidity 0.8+ has built-in checks, but verify for unchecked blocks
- Front-running susceptibility — commit-reveal needed? Deadline parameters?
- Delegate call to untrusted contracts
- Self-destruct / selfdestruct usage
- Uninitialized storage pointers
- tx.origin authentication (should use msg.sender)

**High (should fix):**
- Missing event emissions for state changes
- Missing input validation (zero address, zero amount, bounds)
- Gas optimization issues (storage vs memory, unnecessary SLOAD)
- Floating pragma (should lock to specific version)
- Missing NatSpec documentation on public/external functions

**Medium (recommended):**
- Magic numbers without named constants
- Functions that could be `view` or `pure` but aren't marked
- Missing custom errors (using `require` with strings instead of custom errors)
- Contract size approaching limit (24KB)

### Step 3: Frontend Quality Review

For all TypeScript/React code, check:

**Critical:**
- `any` type usage — must be zero (use proper types)
- Unhandled promise rejections — all async calls need try/catch or error boundaries
- Private keys or secrets in client-side code
- Missing error states — every async operation needs loading/error/success states

**High:**
- TypeScript strict mode enabled in tsconfig.json
- Proper React patterns — no unnecessary re-renders, proper dependency arrays in useEffect
- Accessibility — semantic HTML, ARIA labels, keyboard navigation
- Form validation — client-side validation before contract calls
- Transaction UX — pending states, confirmation, error messages, tx hash links

**Medium:**
- Component organization — proper separation of concerns
- Consistent naming conventions (PascalCase components, camelCase hooks, kebab-case files)
- Unused imports and dead code
- Console.log statements left in production code
- Responsive design — works on mobile

### Step 4: Integra Compliance Review

Check integration with the Integra ecosystem:

**Required:**
- Web3Auth integration — social login works, proper chain configuration. Verify chain ID against `knowledge/networks/mainnet.md` (26217) or `knowledge/networks/testnet.md` (26218) based on project target
- XP event emission — key user actions emit XP events as specified in INTEGRATIONS.md
- Design system — uses correct color palette, typography, component patterns (check against knowledge/design-systems/integra-brand.md for Integra branding, or knowledge/design-systems/custom-brand.md for custom branding -- read .integra/config.json to determine which)
- Contract standards — follows Integra conventions (check against knowledge/conventions.md)
- Token integration — IRL (native) + tUSDI (ERC-20) supported per `knowledge/networks/tokens.md`. Token addresses configurable, not hardcoded. Correct tokens for dApp category per Token Selection Guide
- Token display — balances shown in header/sidebar, proper decimal handling (18 decimals), locale-aware formatting

**Recommended:**
- Faucet link — testnet apps should have a "Get Test IRL + tUSDI" option
- Domain router — configured for `appname.integra.zone`
- Explorer links — transaction hashes link to Integra explorer
- Error messages reference Integra-specific issues (wrong chain, insufficient IRL, etc.)
- Network badge — testnet/mainnet indicator visible at all times
- Zero-balance onboarding — detect empty wallet, guide to faucet

### Step 4b: UI/UX Quality Review

Check against `knowledge/best-practices/` guides:

**Design Adaptation (check against `knowledge/best-practices/design-adaptation.md`):**
- Design mood matches dApp category (DeFi=trust, Gaming=energy, NFT=gallery, etc.)
- Color choices are psychologically appropriate for the use case
- Layout density matches category expectations

**Accessibility (check against `knowledge/best-practices/accessibility.md`):**
- Color contrast meets WCAG AA (4.5:1 body, 3:1 large text)
- Keyboard navigation works for all interactive elements
- Focus indicators visible
- Semantic HTML used throughout
- ARIA labels on icon-only buttons
- `prefers-reduced-motion` supported

**Onboarding UX (check against `knowledge/best-practices/onboarding-ux.md`):**
- First-time experience is clear and welcoming
- Web3Auth social login prominent
- Zero-balance state handled gracefully
- Error messages are user-friendly (not raw blockchain errors)
- Transaction UX follows pending→submitted→confirmed flow

**Performance (check against `knowledge/best-practices/performance.md`):**
- Initial bundle < 200KB gzipped
- Server Components used where possible
- Images use `next/image`
- No unnecessary re-renders

### Step 5: Test Coverage Review

Analyze test quality:
- Contract test coverage — every public/external function should have tests
- Happy path AND failure cases for each function
- Edge cases — zero values, max values, unauthorized callers
- Integration tests — contract interactions that span multiple calls
- Frontend tests — at minimum, component render tests

Run `npx hardhat coverage` if available and show coverage percentages.

### Step 6: Documentation Review

Check completeness:
- README.md exists with setup instructions, usage, and deployment
- docs/PRD.md describes the product
- docs/ARCHITECTURE.md describes the system
- docs/CONTRACTS.md describes contract interfaces
- NatSpec on all public Solidity functions
- JSDoc on complex TypeScript functions
- .env.example with all required variables documented

## Report Format

Present findings as a structured report:

```
Code Review Report — [Project Name]
Date: YYYY-MM-DD

Overall Grade: A/B/C/D/F

Summary:
  Contracts:      X findings (Y critical, Z high)
  Frontend:       X findings (Y critical, Z high)
  Integra:        X findings (Y required missing)
  Tokens:         IRL ✓/✗ | tUSDI ✓/✗ | WIRL ✓/✗
  UI/UX:          X findings (accessibility, design adaptation, onboarding)
  Performance:    Bundle Xkb | LCP Xs | CLS X
  Tests:          X% coverage
  Docs:           X/Y documents present

Critical Findings (must fix before deploy):
  [C1] contracts/Foo.sol:42 — Reentrancy in withdraw()
       Fix: Add nonReentrant modifier and move state change before external call

  [C2] src/hooks/useSwap.ts:15 — Unhandled error in contract call
       Fix: Wrap in try/catch, show error toast to user

High Findings (should fix):
  [H1] ...

Medium Findings (recommended):
  [M1] ...

Integra Compliance:
  [x] Web3Auth integration
  [x] XP events configured
  [ ] Design system — missing Integra colors on sidebar
  [ ] Faucet link — not present

Recommendations:
  1. ...
  2. ...
  3. ...
```

## Post-Review

After showing the report:
1. Use AskUserQuestion:
   "Want to fix any of these findings now?

   1. Yes, fix critical findings first
   2. Yes, fix all findings
   3. No, I'll handle it myself"
2. If yes, prioritize critical findings and delegate fixes to the executor agent
3. After fixes, re-run the affected checks to verify
4. **Run refinement loop** — Use AskUserQuestion: "Before we wrap up the review — anything else you'd like me to check, clarify, or dig deeper into?" If yes, investigate and report. Repeat until satisfied.
5. Offer to run `/integra-studio:deploy` if all critical findings are resolved
