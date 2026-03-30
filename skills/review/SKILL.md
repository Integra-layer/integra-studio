---
name: review
description: Review your dApp code for quality, security, and Integra compatibility.
user_invocable: true
trigger: "integra-studio:review"
---

# Review Skill

Comprehensive code review of an Integra dApp project. Checks security, quality, ecosystem compliance, test coverage, and documentation.

## Activation

When invoked, read the full project to understand scope, then delegate to the **reviewer** agent for analysis.

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
- Web3Auth integration — social login works, proper chain configuration (Chain ID 26217)
- XP event emission — key user actions emit XP events as specified in INTEGRATIONS.md
- Design system — uses Integra color palette, typography, component patterns (check against knowledge/design-system.md)
- Contract standards — follows Integra conventions (check against knowledge/conventions.md)

**Recommended:**
- Faucet link — testnet apps should have a "Get Test IRL" option
- Domain router — configured for `appname.integra.zone`
- Explorer links — transaction hashes link to Integra explorer
- Error messages reference Integra-specific issues (wrong chain, insufficient IRL, etc.)

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
  Contracts: X findings (Y critical, Z high)
  Frontend:  X findings (Y critical, Z high)
  Integra:   X findings (Y required missing)
  Tests:     X% coverage
  Docs:      X/Y documents present

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
1. Ask if the user wants to fix any findings now
2. If yes, prioritize critical findings and delegate fixes to the executor agent
3. After fixes, re-run the affected checks to verify
4. Offer to run `/integra-studio:deploy` if all critical findings are resolved
