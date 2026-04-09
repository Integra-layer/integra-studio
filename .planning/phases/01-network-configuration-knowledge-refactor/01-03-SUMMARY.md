---
phase: 01-network-configuration-knowledge-refactor
plan: 03
subsystem: templates
tags: [dual-network, hardhat, env, web3auth, tailwind, templates]
dependency_graph:
  requires: [01-01]
  provides: [dual-network-templates, correct-brand-tokens-in-templates]
  affects: [generated-dapp-projects]
tech_stack:
  added: []
  patterns: [env-driven-network-selection, conditional-chain-config]
key_files:
  created: []
  modified:
    - templates/env.example.template
    - templates/web3auth-provider.tsx.template
    - templates/hardhat.config.ts.template
  verified:
    - templates/tailwind-integra.ts.template
decisions:
  - Used NEXT_PUBLIC_NETWORK env var as single toggle for mainnet/testnet across all templates
  - Both etherscan customChains entries point to blockscout.integralayer.com (single explorer for both networks)
metrics:
  duration: 84s
  completed: 2026-04-09T10:16:48Z
  tasks_completed: 3
  tasks_total: 3
requirements_completed: [NET-13, NET-14, CONFIG-04, CONFIG-05, CONFIG-06, CONFIG-07]
---

# Phase 01 Plan 03: Template Dual-Network Update Summary

Dual-network env/hardhat/web3auth templates with correct RPC URLs, chain IDs, gas prices, and Coral brand color -- tailwind template verified correct.

## What Was Done

### Task 1: env.example.template + web3auth-provider.tsx.template (d0f1573)

**env.example.template** -- Complete rewrite:
- Added `NEXT_PUBLIC_NETWORK` selector (mainnet/testnet)
- Separate mainnet section: `INTEGRA_MAINNET_RPC_URL`, chain ID 26217, blockscout explorer
- Separate testnet section: `INTEGRA_TESTNET_RPC_URL`, chain ID 26218, blockscout explorer
- `NEXT_PUBLIC_WEB3AUTH_NETWORK` with sapphire_devnet default and warning about wallet address changes
- Updated XP API URL to `api.integralayer.com/xp`

**web3auth-provider.tsx.template** -- Network-aware rewrite:
- Chain ID dynamically selected: `0x6669` (mainnet) or `0x666A` (testnet) based on `NEXT_PUBLIC_NETWORK`
- RPC target dynamically selected with env var fallbacks to correct URLs
- Web3Auth network from `NEXT_PUBLIC_WEB3AUTH_NETWORK` env var (supports sapphire_mainnet and sapphire_devnet)
- Theme color changed from `#6C5CE7` (old purple) to `#FF6D49` (Coral)
- Block explorer updated to `blockscout.integralayer.com`
- Display name dynamically shows "Integra Mainnet" or "Integra Testnet"

### Task 2: hardhat.config.ts.template (879a4df)

- Added `integra-mainnet` network: chain ID 26217, `mainnet.integralayer.com/evm`, gasPrice 5 Twei
- Added `integra-testnet` network: chain ID 26218, `testnet.integralayer.com/evm`, no gasPrice (free)
- Both etherscan customChains use `blockscout.integralayer.com`
- Removed single testnet-only config with deprecated `testnet-rpc.integralayer.com`

### Task 3: tailwind-integra.ts.template (verified, no changes)

- Confirmed `#FF6D49` (Coral) as primary brand color
- Confirmed `Euclid Circular B` as font family
- Confirmed `#0A0A0F` as background color
- No deprecated colors found (`#6C5CE7`, `#A29BFE`, `#0A0A1A`, `#12122A` all absent)

## Deviations from Plan

None -- plan executed exactly as written.

## Decisions Made

1. **NEXT_PUBLIC_NETWORK as single toggle** -- One env var controls chain ID, RPC, display name, and Web3Auth network across all templates. Simpler than per-value overrides.
2. **Shared blockscout explorer** -- Both mainnet and testnet etherscan entries point to `blockscout.integralayer.com` since Integra uses a single Blockscout instance.

## Threat Mitigations Applied

| Threat ID | Mitigation |
|-----------|------------|
| T-01-05 | Mainnet gasPrice set to `5_000_000_000_000` (5 Twei) in hardhat config |
| T-01-06 | Web3Auth network conditional: sapphire_mainnet for mainnet, sapphire_devnet for testnet |
| T-01-07 | Accepted -- DEPLOYER_PRIVATE_KEY empty by default, no secrets in template |

## Self-Check: PASSED

All files verified present. All commit hashes verified in git log.
