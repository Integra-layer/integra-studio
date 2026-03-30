---
name: build
description: Execute the project plan phase by phase. Builds contracts, frontend, integrations with user approval at each step.
user_invocable: true
trigger: "integra-studio:build"
---

# Build Skill

Execute the dApp project plan phase by phase, building contracts, frontend, and integrations with explicit user approval at each step.

## CRITICAL: Use AskUserQuestion for ALL interactions

**Every approval gate, decision point, and user confirmation MUST use the `AskUserQuestion` tool.** Never output a question as plain text. Always use AskUserQuestion so the user gets a proper interactive prompt.

Examples:
- Phase approval: `AskUserQuestion(question: "Phase 1 plan ready. Proceed? (yes / edit / skip)")`
- Decision: `AskUserQuestion(question: "Which approach?\n\n1. Proxy pattern (upgradeable)\n2. Direct deploy (simpler)")`
- Iteration: `AskUserQuestion(question: "Contracts compiled. Review the code and tell me: proceed to frontend, or make changes?")`

## Prerequisites

Before running this skill, verify:
1. The `/start` skill has been run and docs/ folder exists with: PRD.md, ARCHITECTURE.md, CONTRACTS.md, FRONTEND.md, INTEGRATIONS.md
2. The project directory has been initialized (package.json, hardhat.config.ts, next.config.ts exist)
3. Read ALL docs in the docs/ folder to understand what needs to be built

If prerequisites are missing, tell the user to run `/integra-studio:start` first.

## Phase Execution Protocol

For EACH phase below, follow this exact sequence:

1. **Show Plan** — Display what will be built in this phase as a numbered list with file paths
2. **Ask Approval** — Ask the user: "Ready to build Phase N? Any changes before I start?" Wait for confirmation.
3. **Execute** — Delegate to the executor agent. Build all files for the phase.
4. **Show Results** — Display what was created, any compilation output, test results
5. **Ask to Proceed** — Ask: "Phase N complete. Review the output above. Want to proceed to Phase N+1, or iterate on something?"
6. **Iterate if Needed** — If user wants changes, make them and re-show results before moving on

NEVER skip phases. NEVER proceed without user approval.

## Phase 1: Smart Contracts

Read docs/CONTRACTS.md for the contract specifications.

Build:
- `contracts/` directory with all Solidity files specified in CONTRACTS.md
- Each contract with full NatSpec documentation
- `test/` directory with Hardhat tests for every contract function
- Deployment scripts in `ignition/modules/`

Verification:
- Run `npx hardhat compile` — must succeed with zero errors
- Run `npx hardhat test` — all tests must pass
- Show compilation output and test results to user

Delegate to: **executor** agent (model=opus for complex contract logic)

## Phase 2: Frontend Scaffold

Read docs/FRONTEND.md for page specifications and docs/ARCHITECTURE.md for the tech stack.

Build:
- Next.js app directory structure with all pages from FRONTEND.md
- Layout with sidebar navigation, header, and content area
- Web3Auth integration (social login + wallet connection)
- Placeholder components for each page (proper TypeScript, no `any`)
- Tailwind CSS configuration with Integra design system colors
- shadcn/ui component installations

Verification:
- Run `npm run build` — must succeed
- Run `npm run dev` and confirm no console errors
- Show the page list and routing structure to user

Delegate to: **executor** agent

## Phase 3: Contract Integration

Read docs/INTEGRATIONS.md for integration specifications.

Build:
- ABI exports from compiled contracts into `src/lib/abis/`
- Contract address configuration in `src/lib/contracts.ts`
- React hooks for each contract interaction in `src/hooks/`
  - useContractRead hooks for view functions
  - useContractWrite hooks for state-changing functions
  - Proper error handling, loading states, and transaction receipts
- wagmi/viem client configuration
- Connect hooks to frontend pages — replace placeholders with real data

Verification:
- TypeScript compilation passes (`npx tsc --noEmit`)
- All hooks have proper types (no `any`)
- Show the integration map: which hook connects to which contract function

Delegate to: **executor** agent

## Phase 4: UI Polish

Read docs/FRONTEND.md for design specifications. Read knowledge/design-system.md for Integra design tokens.

Build:
- Apply Integra design system colors, typography, spacing
- Loading states (skeletons, spinners) for all async operations
- Error states with user-friendly messages and retry buttons
- Empty states for lists and tables
- Toast notifications for transaction success/failure
- Responsive layout (mobile-first)
- Dark theme as default (Integra standard)
- Subtle animations (page transitions, hover effects, list animations)

Verification:
- Visual review of each page
- Check responsive behavior at mobile/tablet/desktop breakpoints
- Confirm dark theme works properly
- Show before/after comparison if applicable

Delegate to: **executor** agent

## Phase 5: XP and Ecosystem Integration

Read docs/INTEGRATIONS.md for XP event specifications.

Build:
- XP event emission for key user actions (as specified in INTEGRATIONS.md)
- XP display component showing user's earned XP
- Leaderboard integration (if specified)
- Domain router configuration for `appname.integra.zone` subdomain
- Faucet link/integration for testnet IRL
- Any other ecosystem integrations specified in INTEGRATIONS.md

Verification:
- XP events fire correctly on user actions
- XP display shows correct values
- All ecosystem integrations connect properly

Delegate to: **executor** agent

## Phase 6: Testing and Review

Build:
- End-to-end test scenarios in `test/e2e/`
- Integration tests for contract-frontend connection
- Run the full test suite

Then delegate to **reviewer** agent for:
- Contract security review (reentrancy, access control, overflow)
- Frontend quality review (TypeScript strict, error handling, accessibility)
- Integra compliance review (XP events, Web3Auth, design system)
- Documentation completeness check

Show the full review report to the user with findings sorted by severity.

## Completion

When all 6 phases are complete:
1. Show a summary of everything that was built
2. Show the project structure tree
3. Recommend next steps (deploy to testnet, add features, etc.)
4. Offer to run `/integra-studio:deploy` to deploy the dApp
