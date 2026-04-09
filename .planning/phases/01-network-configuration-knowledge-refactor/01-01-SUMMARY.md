---
phase: 01-network-configuration-knowledge-refactor
plan: 01
subsystem: knowledge-base
tags: [network-config, knowledge-files, chain-data]
dependency_graph:
  requires: []
  provides: [mainnet-config, testnet-config, shared-config, precompile-addresses, token-info]
  affects: [CLAUDE.md, integra-ecosystem.md, hardhat-config-template, env-example-template]
tech_stack:
  added: []
  patterns: [knowledge-file-pattern, markdown-table-config]
key_files:
  created:
    - knowledge/networks/mainnet.md
    - knowledge/networks/testnet.md
    - knowledge/networks/shared.md
  modified: []
decisions:
  - "Used markdown table format for all config values — consistent, grep-able, human-readable"
  - "Separated mainnet/testnet/shared into 3 files to avoid conditional logic and cross-contamination"
metrics:
  duration: 67s
  completed: 2026-04-09T10:07:42Z
  tasks_completed: 3
  tasks_total: 3
---

# Phase 01 Plan 01: Network Knowledge Files Summary

Three authoritative network knowledge files created as single source of truth for all Integra chain configuration, eliminating the triple-source-of-truth problem (CLAUDE.md vs integra-ecosystem.md vs inline agent configs).

## Task Results

| Task | Name | Commit | Key Files |
|------|------|--------|-----------|
| 1 | Create mainnet.md | 4ec445d | knowledge/networks/mainnet.md |
| 2 | Create testnet.md | 3084747 | knowledge/networks/testnet.md |
| 3 | Create shared.md | 3bd23e1 | knowledge/networks/shared.md |

## Key Data Points Established

**Mainnet:** Chain ID 26217 (0x6669), Cosmos ID `integra_26217-1`, 5 Twei min gas, sapphire_mainnet Web3Auth

**Testnet:** Chain ID 26218 (0x666A), Cosmos ID `integra-testnet-1`, free gas, sapphire_devnet Web3Auth, faucet 10 IRL + 1,000 tUSDI

**Shared:** IRL/airl token (EVM 18 decimals, Cosmos 6 decimals), WIRL at 0x5002...0001, 9 precompiles (0x800-0x808), CometBFT DPoS consensus, Cosmos SDK v0.53.5 + go-ethereum v1.15.11

## Critical Corrections Applied

1. **Faucet amount:** Corrected from "1000 IRL" to "10 IRL + 1,000 tUSDI"
2. **RPC URL format:** Uses `testnet.integralayer.com/evm` (not deprecated `testnet-rpc.integralayer.com`)
3. **Dual decimal warning:** Documented EVM 18 vs Cosmos SDK 6 decimal mismatch as critical callout

## Deviations from Plan

None -- plan executed exactly as written.

## Verification Results

All automated checks passed:
- All 3 files exist with correct content
- Zero references to deprecated `testnet-rpc.integralayer.com`
- Zero references to incorrect "1000 IRL" faucet amount
- No cross-contamination (mainnet file has no testnet chain IDs, vice versa)

## Self-Check: PASSED

All 3 knowledge files exist. All 3 commit hashes verified. SUMMARY.md created.
