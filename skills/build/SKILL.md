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
2. **Ask Approval** — Use AskUserQuestion:
   "Ready to build Phase {N}: {phase name}?

   1. Yes, proceed
   2. Show me the detailed plan first
   3. Skip this phase"
3. **Execute** — Delegate to the executor agent. Build all files for the phase.
4. **Show Results** — Display what was created, any compilation output, test results
5. **Ask to Proceed** — Use AskUserQuestion:
   "Phase {N} complete. What next?

   1. Proceed to Phase {N+1}
   2. Make changes to what was built
   3. Review what was built in detail"
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
Read `.integra/config.json` for the `ui_generation` choice (`"stitch"` or `"manual"`).

### Stitch AI Pipeline (when `ui_generation` is `"stitch"`)

Before scaffolding, check if Google Stitch is available:

1. **Check availability:** Verify `STITCH_API_KEY` environment variable is set and Stitch MCP tools are accessible
2. **If available:** Delegate to **frontend-designer** agent with Stitch workflow:
   - Frontend-designer generates screens via `mcp__stitch__build_site`
   - Captures screenshots via `mcp__stitch__get_screen_image` for variant review
   - Presents 1-3 style variants to user via AskUserQuestion:
     "Which UI style do you prefer for the main screens?

     1. Minimal — clean, lots of whitespace
     2. Bold — high contrast, prominent CTAs
     3. Data-dense — compact tables, information-rich"
   - Exports selected variant HTML via `mcp__stitch__get_screen_code`
   - Includes Stitch reference HTML in the Frontend Design Document
3. **If unavailable:** Log `"Stitch unavailable -- using manual UI pipeline"` and fall back to the manual pipeline below. Do NOT show an error to the user.

> Reference: `knowledge/stitch-integration.md` for full Stitch integration details

### Manual Pipeline (when `ui_generation` is `"manual"` or Stitch unavailable)

Delegate to **frontend-designer** agent with standard workflow (component tree + wireframe descriptions).

### Build (both pipelines)

- Next.js app directory structure with all pages from FRONTEND.md
- Layout with sidebar navigation, header, and content area
- Web3Auth integration (social login + wallet connection)
- Placeholder components for each page (proper TypeScript, no `any`)
- Tailwind CSS configuration with design system colors (from integra-brand.md or custom-brand.md)
- shadcn/ui component installations
- If Stitch was used: executor uses the Stitch reference HTML as visual specification for component layout and styling

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

> Pipeline reference: `knowledge/ui-skill-pipeline.md`

Read `.integra/config.json` for the branding choice. Based on the `branding` field:
- If `"integra"`: Read `knowledge/design-systems/integra-brand.md` for official Integra design tokens (Coral #FF6D49, Euclid Circular B). Apply the official palette verbatim.
- If `"custom"`: Read `knowledge/design-systems/custom-brand.md` for the AI-generated design system. If the file has placeholder sections, invoke the ui-ux-pro-max skill to generate the custom design system first.

Also read docs/FRONTEND.md for page-level design specifications.

Use AskUserQuestion:
"Applying {branding choice} design system to your UI. Confirm approach?

1. Yes, apply the design system
2. Let me review the design tokens first
3. Switch to the other branding option"

### Skill Pipeline Execution

Run the appropriate pipeline based on branding choice:

**Integra branding (lean pipeline):**
1. Apply `web-design-guidelines` -- verify layout spacing and typography scale
2. Apply `shadcn-ui` -- ensure all components use shadcn/ui base with Integra theme tokens
3. Apply `ui-animation` -- add subtle transitions (150-300ms hover, page transitions, list animations)
4. Apply `taste-skill` -- final polish pass for spacing rhythm, visual hierarchy, content density

**Custom branding (full pipeline):**
1. Apply `web-design-guidelines` -- verify layout spacing and typography scale
2. Invoke `ui-ux-pro-max` -- generate unique design system (if custom-brand.md still has placeholders)
3. Apply `taste-skill` -- ensure custom palette has premium feel (spacing, hierarchy, polish)
4. Apply `shadcn-ui` -- build components with custom theme tokens from custom-brand.md
5. Apply `ui-animation` -- add subtle transitions matching the custom design mood

### Build (both pipelines)

- Apply design system colors, typography, spacing from the selected branding source
- Loading states (skeletons, spinners) for all async operations
- Error states with user-friendly messages and retry buttons
- Empty states for lists and tables
- Toast notifications for transaction success/failure
- Responsive layout (mobile-first)
- Dark theme as default
- Subtle animations per ui-animation skill (page transitions, hover effects, list animations)

### Verification

- Visual review of each page against the design system tokens
- Check responsive behavior at mobile (375px), tablet (640px), desktop (1024px+)
- Confirm dark theme works properly with correct surface layering
- Verify all animations are subtle and purposeful (no flashy effects)
- Run quality checklist from `knowledge/ui-skill-pipeline.md` Section 6
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
3. **Run refinement loop** — Use AskUserQuestion: "Before we wrap up the build — anything you'd like to add, tweak, or clarify? Any grey areas or features I should dig into?" If yes, ask follow-up questions to cover those gaps, implement changes, then ask again. Repeat until satisfied.
4. Recommend next steps (deploy to testnet, add features, etc.)
5. Offer to run `/integra-studio:deploy` to deploy the dApp
