---
name: deploy
description: Deploy your dApp to Integra testnet — contracts, frontend, subdomain.
user_invocable: true
trigger: "integra-studio:deploy"
---

# Deploy Skill

Deploy the complete dApp to Integra: contracts, frontend, and subdomain configuration. Supports both mainnet and testnet targets.

## CRITICAL: Use AskUserQuestion for ALL interactions

**Every approval gate, decision point, and user confirmation MUST use the `AskUserQuestion` tool.** Never output a question as plain text. Always use AskUserQuestion so the user gets a proper interactive prompt.

## Network Target

Read the project's `.integra/config.json` for the target network, or accept a `--network` flag.
Default: testnet. For mainnet deployment, the skill should:
- Read `knowledge/networks/mainnet.md` for chain ID (26217), RPC URL, gas price
- Warn about mainnet gas costs (min 5 Twei per transaction)
- Use `sapphire_mainnet` for Web3Auth verification

For testnet deployment:
- Read `knowledge/networks/testnet.md` for chain ID (26218), RPC URL
- Gas is free on testnet
- Use `sapphire_devnet` for Web3Auth verification

Read `knowledge/networks/shared.md` for token info (IRL/airl), WIRL address, precompile addresses, and decimal conversion warnings. Never hardcode chain IDs, RPC URLs, or explorer URLs.

Read `knowledge/networks/tokens.md` for the full token registry. Ensure deployed contracts have correct token addresses:
- tUSDI (testnet): `0xa640d8b5c9cb3b989881b8e63b0f30179c78a04f`
- WIRL: `0x5002000000000000000000000000000000000001`
- For mainnet: USDI address TBD (replace tUSDI address in environment variables)

## Prerequisites

Before deploying, verify ALL of the following:
1. Contracts compile (`npx hardhat compile` succeeds)
2. All tests pass (`npx hardhat test` succeeds)
3. Frontend builds (`npm run build` succeeds)
4. `.env` file exists with required variables (see Pre-flight Checks below)
5. The user has sufficient IRL in their deployer wallet (testnet: use faucet; mainnet: real IRL needed)

If any prerequisite fails, stop and help the user fix it before proceeding.

## Pre-flight Checks

Run these checks and show results as a checklist:

```
Pre-flight Checklist:
[x] Contracts compile — 3 contracts, 0 errors
[x] Tests pass — 24/24 passing
[x] Frontend builds — no errors
[x] .env configured:
    [x] DEPLOYER_PRIVATE_KEY set
    [x] INTEGRA_RPC_URL set (from knowledge/networks/ for target network)
    [x] NEXT_PUBLIC_WEB3AUTH_CLIENT_ID set
    [x] NEXT_PUBLIC_CHAIN_ID set (26217 for mainnet, 26218 for testnet -- see knowledge/networks/)
[ ] Deployer wallet funded — checking...
```

For the wallet check, if we can't verify on-chain, tell the user:
"Make sure your deployer wallet has sufficient IRL for gas. Testnet: use the faucet at https://faucet.integralayer.com (10 IRL + 1,000 tUSDI per request, 24h cooldown). Mainnet: you need real IRL (min gas price is 5 Twei)."

If any check fails, show exactly what's wrong and how to fix it. Don't proceed until all checks pass.

### Network Confirmation

Use AskUserQuestion:
"Deploying to {network} (Chain ID {chainId}). Confirm?

1. Yes, deploy to {network}
2. Switch to {other network}
3. Cancel deployment"

## Deployment Steps

### Step 1: Deploy Contracts to Testnet

Use Hardhat Ignition for deployment:

```bash
npx hardhat ignition deploy ignition/modules/Deploy.ts --network integra-testnet
```

After deployment:
- Capture all deployed contract addresses from the output
- Show the user each contract name and its address
- Save addresses to `deployments/testnet.json`:
  ```json
  {
    "network": "integra-testnet",
    "chainId": "<from knowledge/networks/ target>",
    "deployedAt": "2026-03-30T12:00:00Z",
    "contracts": {
      "ContractName": {
        "address": "0x...",
        "txHash": "0x..."
      }
    }
  }
  ```

Delegate to: **deployer** agent

### Step 2: Verify Contracts on Explorer

For each deployed contract:

```bash
npx hardhat verify --network integra-testnet <address> <constructor-args>
```

If verification fails (explorer may not support it yet), note it but don't block deployment.

Show verification status for each contract.

### Step 3: Update Environment with Deployed Addresses

Update the following files with the new contract addresses:
- `.env` — add `NEXT_PUBLIC_<CONTRACT_NAME>_ADDRESS=0x...` for each contract
- `.env` — ensure `NEXT_PUBLIC_TUSDI_ADDRESS=0xa640d8b5c9cb3b989881b8e63b0f30179c78a04f` (testnet) or `NEXT_PUBLIC_USDI_ADDRESS=0x...` (mainnet)
- `.env` — ensure `NEXT_PUBLIC_WIRL_ADDRESS=0x5002000000000000000000000000000000000001`
- `src/lib/contracts.ts` — update the address constants (including token addresses)
- `deployments/testnet.json` — already done in Step 1

Show the user the updated values.

### Step 4: Build Frontend

```bash
npm run build
```

Must succeed. If it fails after address updates, there's a configuration issue — debug and fix it.

### Step 5: Deploy Frontend

Use AskUserQuestion:
"How should we deploy the frontend?

1. Docker (recommended for persistent hosting)
2. Static export (for simple hosting)
3. Vercel (easiest for demos)
4. Let me describe a different approach"

Based on the user's choice:

**If 1 (Docker):**
- Generate a `Dockerfile` and `docker-compose.yml` if they don't exist
- Build the Docker image
- Provide instructions for running on a VPS

**If 2 (Static Export):**
- Run `next build` with `output: 'export'` if the app supports it
- Generate static files in `out/`
- Provide instructions for hosting on any static host

**If 3 (Vercel):**
- Check if `vercel` CLI is available
- Run `vercel --prod` if available
- Otherwise provide manual Vercel deployment instructions

**If 4 (Custom):** Follow the user's described approach.

### Step 6: Configure Subdomain

If the Integra domain router supports it:
- Register `appname.integra.zone` subdomain
- Point it to the frontend deployment
- Show the user the URL

If domain router is not yet available:
- Note this step as pending
- Tell the user they can configure it later when the router is live

### Step 7: Smoke Tests

After deployment, run basic smoke tests:
- Can the frontend load?
- Can it connect to the deployed contracts?
- Can it read contract state?
- Can it submit a transaction? (if possible without real funds)

Show results for each check.

### Step 8: Deployment Summary

Display a comprehensive summary:

```
Deployment Complete!

Network: Integra <Mainnet|Testnet> (Chain ID: <from knowledge/networks/>)
Deployed at: 2026-03-30 12:00:00 UTC

Contracts:
  ContractName — 0x1234...abcd (verified)
  OtherContract — 0x5678...efgh (verified)

Frontend:
  URL: https://appname.integra.zone (or Vercel URL)
  Build: Next.js 15 static export

Integrations:
  Web3Auth: Connected
  XP Events: Configured
  Faucet: Linked

Explorer Links:
  ContractName: https://blockscout.integralayer.com/address/0x1234...abcd
  OtherContract: https://blockscout.integralayer.com/address/0x5678...efgh

Next Steps:
  1. Test the live app at the URL above
  2. Share with team for feedback
  3. Switch networks with --network mainnet|testnet (reads from knowledge/networks/)
```

## Post-Deployment Refinement

After showing the deployment summary, use AskUserQuestion:

"Before we wrap up — anything you'd like to add, change, or investigate? Any grey areas about the deployment I should dig into?"

If yes, ask follow-up questions via AskUserQuestion, address gaps, then ask again. Repeat until satisfied.

## Error Handling

- If contract deployment fails: show the error, suggest fixes (insufficient gas, constructor args wrong, etc.)
- If verification fails: note it but continue — verification is nice-to-have
- If frontend build fails: stop and fix before deploying
- If subdomain config fails: note it but continue — provide direct URL instead
- NEVER leave the user with a partial deployment without clearly stating what succeeded and what failed
