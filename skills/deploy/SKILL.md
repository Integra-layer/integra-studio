---
name: deploy
description: Deploy your dApp to Integra testnet — contracts, frontend, subdomain.
user_invocable: true
trigger: "integra-studio:deploy"
---

# Deploy Skill

Deploy the complete dApp to Integra testnet: contracts, frontend, and subdomain configuration.

## CRITICAL: Use AskUserQuestion for ALL interactions

**Every approval gate, decision point, and user confirmation MUST use the `AskUserQuestion` tool.** Never output a question as plain text. Always use AskUserQuestion so the user gets a proper interactive prompt.

## Prerequisites

Before deploying, verify ALL of the following:
1. Contracts compile (`npx hardhat compile` succeeds)
2. All tests pass (`npx hardhat test` succeeds)
3. Frontend builds (`npm run build` succeeds)
4. `.env` file exists with required variables (see Pre-flight Checks below)
5. The user has testnet IRL in their deployer wallet

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
    [x] INTEGRA_TESTNET_RPC_URL set (default: https://testnet-rpc.integralayer.com)
    [x] NEXT_PUBLIC_WEB3AUTH_CLIENT_ID set
    [x] NEXT_PUBLIC_CHAIN_ID set (26217)
[ ] Deployer wallet funded — checking...
```

For the wallet check, if we can't verify on-chain, tell the user:
"Make sure your deployer wallet has at least 1 IRL for gas. Use the faucet at https://faucet.integralayer.com if needed."

If any check fails, show exactly what's wrong and how to fix it. Don't proceed until all checks pass.

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
    "chainId": 26217,
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
- `src/lib/contracts.ts` — update the address constants
- `deployments/testnet.json` — already done in Step 1

Show the user the updated values.

### Step 4: Build Frontend

```bash
npm run build
```

Must succeed. If it fails after address updates, there's a configuration issue — debug and fix it.

### Step 5: Deploy Frontend

Offer the user deployment options:

**Option A: Docker (recommended for persistent hosting)**
- Generate a `Dockerfile` and `docker-compose.yml` if they don't exist
- Build the Docker image
- Provide instructions for running on a VPS

**Option B: Static Export (for simple hosting)**
- Run `next build` with `output: 'export'` if the app supports it
- Generate static files in `out/`
- Provide instructions for hosting on any static host

**Option C: Vercel (easiest)**
- Check if `vercel` CLI is available
- Run `vercel --prod` if available
- Otherwise provide manual Vercel deployment instructions

Let the user pick. Default recommendation is Option A for production apps, Option C for demos.

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

Network: Integra Testnet (Chain ID: 26217)
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
  ContractName: https://explorer.integralayer.com/address/0x1234...abcd
  OtherContract: https://explorer.integralayer.com/address/0x5678...efgh

Next Steps:
  1. Test the live app at the URL above
  2. Share with team for feedback
  3. When ready for mainnet, run with --network integra-mainnet
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
