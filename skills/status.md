---
name: status
description: Check project status — what's built, what's remaining, next steps.
user_invocable: true
trigger: "integra-studio:status"
---

# Status Skill

Quick project status overview. Shows what exists, what's missing, and recommends the next action.

## Activation

When invoked, scan the project directory and show a comprehensive status report. No user input needed — just read and report.

## Status Checks

### 1. Documentation Status

Check for the existence of each doc file:

| Document | Path | Status |
|----------|------|--------|
| PRD | docs/PRD.md | Present / Missing |
| Architecture | docs/ARCHITECTURE.md | Present / Missing |
| Contracts Spec | docs/CONTRACTS.md | Present / Missing |
| Frontend Spec | docs/FRONTEND.md | Present / Missing |
| Integrations Spec | docs/INTEGRATIONS.md | Present / Missing |
| README | README.md | Present / Missing |

If ALL docs are missing: recommend running `/integra-studio:start`
If SOME docs are missing: note which ones and suggest completing them

### 2. Smart Contracts Status

Check `contracts/` directory:
- List each `.sol` file found
- For each contract, check if it compiles: run `npx hardhat compile` (if hardhat is configured)
- Check `test/` directory for corresponding test files
- If `deployments/testnet.json` exists, show deployed addresses

Status per contract:
- **Written** — .sol file exists
- **Compiled** — compiles without errors
- **Tested** — has test file with passing tests
- **Deployed** — has a deployed address in deployments/

### 3. Frontend Status

Check `src/app/` directory:
- List each page directory (route) found
- Check if layout.tsx exists
- Check if `src/components/` has components
- Check if `src/hooks/` has hooks
- Check if Tailwind config includes Integra design system colors
- Run `npm run build` to check if frontend builds (or just check for build errors without full build)

Status per page:
- **Scaffolded** — page.tsx exists with placeholder
- **Integrated** — page uses hooks that connect to contracts
- **Polished** — page has loading/error states and design system applied

### 4. Integration Status

Check for:
- `src/lib/contracts.ts` — contract address configuration
- `src/lib/abis/` — ABI files exported from compilation
- `src/hooks/` — React hooks for contract interactions
- Web3Auth configuration in providers or layout
- XP event integration
- `.env` or `.env.local` with required variables

### 5. Deployment Status

Check for:
- `deployments/testnet.json` — testnet deployment record
- `deployments/mainnet.json` — mainnet deployment record
- `Dockerfile` or `docker-compose.yml`
- Vercel configuration (`vercel.json`)

## Report Format

```
Project Status — [Project Name or directory name]

Documentation:    [3/5 docs] ██████░░░░ 60%
Contracts:        [2/3 compiled, 1/3 tested] ████░░░░░░ 40%
Frontend:         [4/6 pages scaffolded, 2 integrated] █████░░░░░ 50%
Integrations:     [Web3Auth: yes, XP: no, ABIs: yes] ██████░░░░ 60%
Deployment:       [Not deployed]

Overall Progress: ████████░░░░░░░░░░░░ 42%

Details:

  Contracts:
    AssetManager.sol    — Written, Compiled, Tested
    Marketplace.sol     — Written, Compiled, No Tests
    Rewards.sol         — Not Started

  Pages:
    /dashboard          — Scaffolded, Integrated
    /assets             — Scaffolded, Integrated
    /marketplace        — Scaffolded, Placeholder
    /profile            — Scaffolded, Placeholder
    /settings           — Not Started
    /admin              — Not Started

  Integrations:
    [x] Web3Auth configured
    [x] ABI files exported
    [x] Contract hooks created
    [ ] XP events not configured
    [ ] Domain router not configured

  Environment:
    [x] .env exists
    [x] RPC URL configured
    [ ] Web3Auth client ID missing
    [ ] Deployer key not set

Next Recommended Action:
  Write tests for Marketplace.sol, then run /integra-studio:build to continue Phase 3.
```

## Progress Calculation

Calculate overall progress as a weighted average:
- Documentation: 10%
- Contracts (written + compiled + tested): 30%
- Frontend (scaffolded + integrated + polished): 30%
- Integrations: 20%
- Deployment: 10%

## Next Action Logic

Based on what's missing, recommend ONE next action:

| State | Recommendation |
|-------|---------------|
| No docs | Run `/integra-studio:start` to set up the project |
| Docs exist, no contracts | Run `/integra-studio:build` to start Phase 1 |
| Contracts exist, no frontend | Run `/integra-studio:build` to start Phase 2 |
| Frontend exists, no integration | Run `/integra-studio:build` to start Phase 3 |
| Everything built, not reviewed | Run `/integra-studio:review` for code review |
| Everything reviewed, not deployed | Run `/integra-studio:deploy` to deploy |
| Everything deployed | Congratulations! Suggest adding features or iterating |

## Quick Mode

If the user just wants a one-liner status, detect short prompts like "status?" or "where am I?" and give a single-line answer:

"Phase 2 in progress — contracts done, frontend scaffolding next. Run `/integra-studio:build` to continue."
