# Phase 1: Network Configuration & Knowledge Refactor - Research

**Researched:** 2026-04-09
**Domain:** Network configuration architecture, knowledge file refactoring, design system alignment
**Confidence:** HIGH

## Summary

Phase 1 is a **content refactoring phase** -- no new features, no code generation, no external dependencies. The work is entirely about moving data between markdown files, updating hardcoded values in templates and agent prompts, and creating new knowledge files. Every file that needs changing already exists in the repository and has been audited in this research.

The core problem is a **triple source of truth**: network config lives in `CLAUDE.md` (testnet-only, chain ID 26218), `knowledge/integra-ecosystem.md` (contradictory chain IDs -- both mainnet and testnet show 26217), and inline in 8 agent files + 4 templates + 2 skills. These contradict each other on chain IDs, RPC URLs, explorer URLs, faucet amounts, design system colors, and fonts. Phase 1 eliminates this by creating `knowledge/networks/` as the single source and stripping network data from everywhere else.

**Primary recommendation:** Create three new files (`knowledge/networks/mainnet.md`, `testnet.md`, `shared.md`), then systematically update every file that currently inlines network config -- working outward from knowledge files, to CLAUDE.md, to agents, to templates, to skills. Each file change is small and independently verifiable.

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|------------------|
| NET-01 | Dual-network support -- mainnet (26217/0x6669) and testnet (26218/0x666A) | Contradiction inventory below shows current testnet-only config; architecture pattern in ARCHITECTURE.md Section 1 |
| NET-02 | Network selector in wizard -- "Build for mainnet or testnet?" | skills/start/SKILL.md line 158 hardcodes testnet; needs conditional |
| NET-03 | Separate network knowledge files -- mainnet.md and testnet.md | knowledge/networks/ directory does not exist yet; file structure defined in ARCHITECTURE.md |
| NET-04 | Shared network knowledge -- shared.md | Token info (IRL/airl), WIRL address, decimal warnings go here |
| NET-05 | Full endpoint config per network -- EVM JSON-RPC, WebSocket, Cosmos RPC, REST API, gRPC | Currently only EVM RPC documented; need all 5 endpoint types |
| NET-06 | Correct faucet info -- 10 IRL + 1,000 tUSDI per request | Root CLAUDE.md says "1000 IRL per address per 24h" (wrong); .claude/CLAUDE.md same |
| NET-07 | WIRL token address: 0x5002000000000000000000000000000000000001 | Not documented anywhere in current files |
| NET-08 | All 9 precompiled contract addresses (0x800-0x808) | Not documented anywhere in current files |
| NET-09 | Dual decimal precision warning -- EVM 18 vs Cosmos 6 | Partially in PITFALLS.md; not in any knowledge file agents read |
| NET-10 | Cosmos chain IDs -- mainnet: integra_26217-1, testnet: integra-testnet-1 | Not documented in any agent-readable file |
| NET-11 | Explorer URLs -- blockscout (EVM), scan (Cosmos) | Current files use wrong URLs (testnet-explorer, explorer); two explorers not documented |
| NET-12 | Min gas price mainnet: 5 Twei | Not in any agent-readable file |
| NET-13 | Web3Auth sapphire_mainnet for mainnet, sapphire_devnet for testnet | Template hardcodes sapphire_devnet; no conditional logic |
| NET-14 | Remove old testnet-rpc.integralayer.com URL | Found in 14 locations across agents/templates/skills |
| CONFIG-01 | Refactor CLAUDE.md -- move network data out, keep behavior rules only | Root CLAUDE.md has full chain section (lines 7-13) + infrastructure table (lines 26-32) |
| CONFIG-02 | Update .claude/CLAUDE.md to match root CLAUDE.md | .claude/CLAUDE.md has OLD design system (purple #6C5CE7, Inter font) contradicting root |
| CONFIG-03 | Update integra-ecosystem.md -- remove network config, keep products/APIs | Lines 1-57 have chain config table + hardhat/wagmi code with wrong values |
| CONFIG-04 | Update hardhat.config.ts.template for dual-network | Currently testnet-only with chain ID 26218, old RPC URL |
| CONFIG-05 | Update env.example.template with mainnet + testnet vars | Currently has single INTEGRA_RPC_URL, no mainnet section |
| CONFIG-06 | Update web3auth-provider.tsx.template for network-aware sapphire config | Hardcodes 0x666A, testnet-rpc, SAPPHIRE_DEVNET, wrong theme color #6C5CE7 |
| CONFIG-07 | Update tailwind-integra.ts.template with latest brand tokens | Already correct (Coral #FF6D49, Euclid Circular B) -- verify only |
| CONFIG-08 | Update agents to read from knowledge/networks/ instead of inline config | 8 agents with inline network data; see inventory below |
| CONFIG-09 | Deploy skill supports both mainnet and testnet targets | skills/deploy/SKILL.md hardcodes testnet throughout |
| CONFIG-10 | Consensus/block info: ~5s blocks, instant finality (CometBFT DPoS) | integration-planner.md says "~2 seconds" (wrong); needs correction |
</phase_requirements>

## Contradiction Inventory

This is the critical finding. Every contradiction below must be resolved in Phase 1.

### Chain ID Contradictions

| File | Chain ID Used | Correct Value |
|------|--------------|---------------|
| CLAUDE.md (root) | 26218 (testnet only) | Needs both: mainnet 26217, testnet 26218 |
| .claude/CLAUDE.md | No chain ID visible in design section | Needs pointer to knowledge/networks/ |
| knowledge/integra-ecosystem.md | 26217 for BOTH mainnet AND testnet | Mainnet=26217, Testnet=26218 |
| templates/hardhat.config.ts.template | 26218 | Needs both networks |
| templates/web3auth-provider.tsx.template | 0x666A (=26218) | Needs conditional |
| templates/env.example.template | 26218 | Needs both |
| agents/executor.md | 0x666A (=26218) in Web3Auth, 26217 in wagmi | Contradicts itself |
| agents/deployer.md | 26217 | Correct for testnet? No -- testnet is 26218 |
| agents/architect.md | 26217 | Wrong for testnet (26218) |
| agents/integration-planner.md | 26217 | Wrong for testnet (26218) |
| agents/reviewer.md | 26217 | Wrong for testnet (26218) |
| skills/deploy/SKILL.md | 26217 | Wrong for testnet (26218) |
| skills/start/SKILL.md | 26218 | Only testnet, no mainnet |
| skills/review/SKILL.md | 26217 | Wrong for testnet (26218) |

**Key insight:** Most agents say 26217 (which is mainnet), but they claim to be configuring "testnet." The templates say 26218 (correct for testnet). Nobody documents both.

### RPC URL Contradictions

| File | URL Used | Correct Value |
|------|----------|---------------|
| CLAUDE.md (root) | testnet-rpc.integralayer.com | DEPRECATED -- use testnet.integralayer.com/evm |
| knowledge/integra-ecosystem.md | testnet-rpc.integralayer.com | DEPRECATED |
| templates/hardhat.config.ts.template | testnet-rpc.integralayer.com | DEPRECATED |
| templates/web3auth-provider.tsx.template | testnet-rpc.integralayer.com | DEPRECATED |
| templates/env.example.template | testnet-rpc.integralayer.com | DEPRECATED |
| agents/deployer.md | testnet-rpc.integralayer.com | DEPRECATED |
| agents/executor.md | testnet-rpc.integralayer.com | DEPRECATED |
| agents/architect.md | testnet-rpc.integralayer.com | DEPRECATED |
| agents/integration-planner.md | testnet-rpc.integralayer.com | DEPRECATED |
| skills/deploy/SKILL.md | testnet-rpc.integralayer.com | DEPRECATED |

**All 10 occurrences** use the deprecated URL. None use the correct format.

### Explorer URL Contradictions

| File | URL Used | Correct Values |
|------|----------|----------------|
| CLAUDE.md (root) | explorer.integralayer.com | EVM: blockscout.integralayer.com, Cosmos: scan.integralayer.com |
| knowledge/integra-ecosystem.md | testnet-explorer.integralayer.com | Wrong |
| agents/deployer.md | testnet-explorer.integralayer.com | Wrong |
| agents/architect.md | testnet-explorer.integralayer.com | Wrong |
| agents/integration-planner.md | testnet-explorer.integralayer.com | Wrong |
| agents/executor.md | explorer.integralayer.com (one place), testnet-explorer.integralayer.com (another) | Contradicts itself |
| templates/hardhat.config.ts.template | explorer.integralayer.com | Wrong |
| skills/deploy/SKILL.md | explorer.integralayer.com | Wrong |

### Design System Contradictions

| File | Colors | Font | Correct |
|------|--------|------|---------|
| CLAUDE.md (root) | Coral #FF6D49 | Euclid Circular B | CORRECT |
| .claude/CLAUDE.md | Purple #6C5CE7, #A29BFE | Inter | WRONG -- outdated |
| knowledge/design-system.md | Coral #FF6D49 | Euclid Circular B | CORRECT |
| templates/tailwind-integra.ts.template | Coral #FF6D49 | Euclid Circular B | CORRECT |
| templates/web3auth-provider.tsx.template | theme: { primary: "#6C5CE7" } | N/A | WRONG -- should be #FF6D49 |
| agents/frontend-designer.md | indigo-500 (#6366f1), bg-[#0a0a0f], bg-[#12121a] | Inter | WRONG -- old palette |
| agents/architect.md | Not checked in detail | Inter | WRONG font |
| agents/executor.md | References Web3Auth with #6C5CE7 | Inter | WRONG both |
| agents/reviewer.md | indigo-500, bg-[#0a0a0f], bg-[#12121a] | Inter | WRONG -- old palette |

### Faucet Amount Contradictions

| File | Amount | Correct |
|------|--------|---------|
| CLAUDE.md (root) | 1000 IRL per address per 24h | WRONG |
| .claude/CLAUDE.md | 1000 IRL per address per 24h | WRONG |
| knowledge/integra-ecosystem.md | 1 IRL (1e18 airl), 24h cooldown | WRONG |
| knowledge/product-ideas.md | 1000 IRL | WRONG |
| Correct per REQUIREMENTS.md | 10 IRL + 1,000 tUSDI per request | -- |

### Block Time Contradictions

| File | Value | Correct |
|------|-------|---------|
| agents/integration-planner.md | ~2 seconds | WRONG |
| STACK.md research | ~5s blocks, instant finality | CORRECT per CONFIG-10 |

## Architecture Patterns

### Recommended File Structure (New/Changed Files)

```
knowledge/
  networks/
    mainnet.md          # NEW — all mainnet config
    testnet.md          # NEW — all testnet config  
    shared.md           # NEW — cross-network info (IRL/airl, WIRL, decimals, precompiles)
  integra-ecosystem.md  # UPDATED — remove network config section (lines 1-57)
  design-system.md      # UNCHANGED — already correct
  conventions.md        # UNCHANGED
  product-ideas.md      # UPDATED — fix faucet amount

CLAUDE.md               # UPDATED — strip network data, add pointer to knowledge/networks/
.claude/CLAUDE.md       # UPDATED — sync design system with root CLAUDE.md

agents/
  architect.md          # UPDATED — remove inline network config, add "read knowledge/networks/"
  contract-designer.md  # UNCHANGED (no network refs)
  deployer.md           # UPDATED — remove inline config, support dual-network
  discovery.md          # UNCHANGED (no network refs)
  executor.md           # UPDATED — remove inline config, fix design system refs
  frontend-designer.md  # UPDATED — fix design system (colors, font)
  integration-planner.md # UPDATED — remove inline config
  reviewer.md           # UPDATED — fix design system checklist, chain ID refs

templates/
  hardhat.config.ts.template    # UPDATED — dual-network
  env.example.template          # UPDATED — dual-network sections
  web3auth-provider.tsx.template # UPDATED — network-aware, fix theme color
  tailwind-integra.ts.template  # UNCHANGED — already correct

skills/
  start/SKILL.md        # UPDATED — fix chain ID 26218 reference
  deploy/SKILL.md       # UPDATED — support dual-network deployment
  review/SKILL.md       # UPDATED — fix chain ID reference
  explore/SKILL.md      # MINOR — verify faucet info
```

### Pattern: Network Knowledge File Structure

Each network file follows a consistent table-based format for machine-readability by agents. [VERIFIED: architecture research in ARCHITECTURE.md] [VERIFIED: REQUIREMENTS.md NET-03 through NET-12]

```markdown
# Integra Mainnet

## EVM Configuration
| Property | Value |
|----------|-------|
| Chain Name | Integra Mainnet |
| Chain ID (decimal) | 26217 |
| Chain ID (hex) | 0x6669 |
| EVM JSON-RPC | https://mainnet.integralayer.com/evm |
| WebSocket | wss://mainnet.integralayer.com/evm/ws |
| Block Explorer (EVM) | https://blockscout.integralayer.com |

## Cosmos Configuration
| Property | Value |
|----------|-------|
| Cosmos Chain ID | integra_26217-1 |
| Cosmos RPC | https://mainnet.integralayer.com/rpc |
| REST API | https://mainnet.integralayer.com/api |
| gRPC | mainnet.integralayer.com:9090 |
| Block Explorer (Cosmos) | https://scan.integralayer.com |

## Gas
| Property | Value |
|----------|-------|
| Min Gas Price | 5,000,000,000,000 airl (5 Twei) |
| Block Time | ~5s |
| Finality | Instant (CometBFT DPoS) |

## Web3Auth
| Property | Value |
|----------|-------|
| Network | sapphire_mainnet |
| Client ID | (from .env — per-project) |
```

### Pattern: Agent Network Reference

Instead of inline config, agents should contain a one-liner directive:

```markdown
## Network Configuration

Read `knowledge/networks/mainnet.md` or `knowledge/networks/testnet.md` based on the project's 
`.integra/config.json` network setting. Read `knowledge/networks/shared.md` for token info and 
precompile addresses. Never hardcode chain IDs, RPC URLs, or explorer URLs.
```

### Pattern: Template Dual-Network Support

Templates become network-aware with both networks present. The executor agent fills in the correct values based on the wizard's network selection:

```typescript
// hardhat.config.ts.template — BOTH networks always present
networks: {
  "integra-mainnet": {
    url: process.env.INTEGRA_MAINNET_RPC_URL || "https://mainnet.integralayer.com/evm",
    chainId: 26217,
    gasPrice: 5_000_000_000_000,
    accounts: process.env.DEPLOYER_PRIVATE_KEY ? [process.env.DEPLOYER_PRIVATE_KEY] : [],
  },
  "integra-testnet": {
    url: process.env.INTEGRA_TESTNET_RPC_URL || "https://testnet.integralayer.com/evm",
    chainId: 26218,
    accounts: process.env.DEPLOYER_PRIVATE_KEY ? [process.env.DEPLOYER_PRIVATE_KEY] : [],
  },
},
```

### Anti-Patterns to Avoid

- **Config Duplication Across Files:** Same chain ID appearing in CLAUDE.md, agents, templates, and knowledge files. The whole point of this phase is to eliminate this. [VERIFIED: ARCHITECTURE.md Anti-Pattern 1]
- **Monolithic CLAUDE.md:** Putting all network config back into CLAUDE.md. Keep it lean -- rules and routing only. [VERIFIED: ARCHITECTURE.md Anti-Pattern 5]
- **Partial Updates:** Updating templates but forgetting agents, or vice versa. The contradiction inventory above is the checklist.

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Network config storage | Custom JSON config system | Markdown knowledge files in knowledge/networks/ | Claude Code auto-loads knowledge/ files into context; markdown is human-readable and agent-readable |
| Template variable substitution | Template engine (Handlebars, EJS) | Agent reads knowledge file, writes correct values | The agent IS the template engine; no build tooling needed |
| Network validation | Custom validation scripts | Consistent table format in knowledge files | Agents can parse tables; validation is at code-generation time, not runtime |

## Common Pitfalls

### Pitfall 1: Partial File Updates Leave Contradictions
**What goes wrong:** Updating mainnet.md but forgetting to update the deployer agent's inline config. The agent reads its own prompt (with old values) instead of the knowledge file.
**Why it happens:** 14+ files reference network config. Easy to miss one.
**How to avoid:** Use the contradiction inventory above as a checklist. After all updates, grep for deprecated values (`testnet-rpc`, `26218` in wrong places, `#6C5CE7`, `Inter`) to verify nothing was missed.
**Warning signs:** Grep for `testnet-rpc.integralayer.com` returns any hits outside knowledge/networks/testnet.md.

### Pitfall 2: Chain ID 26217 vs 26218 Confusion
**What goes wrong:** Files that say "testnet" but use chain ID 26217 (which is mainnet). Or vice versa.
**Why it happens:** The IDs differ by 1. Many existing files already have this bug.
**How to avoid:** Always pair the chain ID with its network name in a single line. Never write just a number -- always write "26217 (mainnet)" or "26218 (testnet)".
**Warning signs:** Any file where "testnet" and "26217" appear together, or "mainnet" and "26218".

### Pitfall 3: Design System Drift Between .claude/CLAUDE.md and Root
**What goes wrong:** .claude/CLAUDE.md still references old purple palette (#6C5CE7) and Inter font. Agents reading .claude/CLAUDE.md get wrong design direction.
**Why it happens:** Root CLAUDE.md was updated to coral palette but .claude/CLAUDE.md was not synced.
**How to avoid:** CONFIG-02 explicitly requires syncing. After update, diff the two files to verify design system sections match.
**Warning signs:** grep for `#6C5CE7` or `Inter` returns hits in .claude/CLAUDE.md.

### Pitfall 4: Forgetting the Web3Auth Theme Color
**What goes wrong:** web3auth-provider.tsx.template line 58 has `theme: { primary: "#6C5CE7" }` -- the old purple. Generated dApps show purple Web3Auth modals instead of coral.
**Why it happens:** Theme color is buried in a template, not in the design system file.
**How to avoid:** Update to `#FF6D49` (coral) in the template. Add a comment referencing the brand.

### Pitfall 5: Old Explorer URLs Persist
**What goes wrong:** Generated dApps link to `testnet-explorer.integralayer.com` or `explorer.integralayer.com` which are wrong. Correct URLs are `blockscout.integralayer.com` (EVM) and `scan.integralayer.com` (Cosmos).
**Why it happens:** Explorer URLs are scattered across 8+ files.
**How to avoid:** Put explorer URLs ONLY in knowledge/networks/*.md files. Agents and templates reference those files, never hardcode explorer URLs.

## Code Examples

### knowledge/networks/shared.md Structure

```markdown
# Integra Shared Network Configuration

## Native Token
| Property | Value |
|----------|-------|
| Name | IRL |
| Symbol | IRL |
| Smallest Unit | airl |
| EVM Decimals | 18 |
| Cosmos SDK Decimals | 6 |

**CRITICAL: Dual Decimal Warning**
EVM layer uses 18 decimals (1 IRL = 10^18 airl). Cosmos SDK Bank module uses 6 decimals.
Any code bridging between EVM and Cosmos MUST explicitly convert. See PITFALLS.md Pitfall 1.

## WIRL (Wrapped IRL)
| Property | Value |
|----------|-------|
| Address | 0x5002000000000000000000000000000000000001 |
| Standard | ERC-20 |

## Precompiled Contracts
| Name | Address | Purpose |
|------|---------|---------|
| Staking | 0x0000000000000000000000000000000000000800 | Delegate/undelegate/redelegate |
| Distribution | 0x0000000000000000000000000000000000000801 | Claim staking rewards |
| ICS-20 | 0x0000000000000000000000000000000000000802 | IBC token transfers |
| Vesting | 0x0000000000000000000000000000000000000803 | Vesting account management |
| Authorization (authz) | 0x0000000000000000000000000000000000000804 | Grant/revoke authorizations |
| Bank | 0x0000000000000000000000000000000000000805 | Native token transfers |
| Governance | 0x0000000000000000000000000000000000000806 | Submit/vote on proposals |
| Slashing | 0x0000000000000000000000000000000000000807 | Validator slashing queries |
| Evidence | 0x0000000000000000000000000000000000000808 | Double-sign evidence |

## Consensus
| Property | Value |
|----------|-------|
| Engine | CometBFT (DPoS) |
| Block Time | ~5 seconds |
| Finality | Instant (single-slot) |
| SDK Version | Cosmos SDK v0.53.5 |
| EVM Version | go-ethereum v1.15.11 |
```

### Updated env.example.template Structure

```bash
# === NETWORK SELECTION ===
# Set to "mainnet" or "testnet"
NEXT_PUBLIC_NETWORK=testnet

# === MAINNET ===
INTEGRA_MAINNET_RPC_URL=https://mainnet.integralayer.com/evm
NEXT_PUBLIC_MAINNET_CHAIN_ID=26217
NEXT_PUBLIC_MAINNET_EXPLORER_URL=https://blockscout.integralayer.com

# === TESTNET ===
INTEGRA_TESTNET_RPC_URL=https://testnet.integralayer.com/evm
NEXT_PUBLIC_TESTNET_CHAIN_ID=26218
NEXT_PUBLIC_TESTNET_EXPLORER_URL=https://blockscout.integralayer.com

# === DEPLOYER ===
DEPLOYER_PRIVATE_KEY=

# === WEB3AUTH ===
# WARNING: Changing WEB3AUTH_NETWORK changes ALL user wallet addresses
NEXT_PUBLIC_WEB3AUTH_CLIENT_ID=
# sapphire_mainnet for mainnet, sapphire_devnet for testnet
NEXT_PUBLIC_WEB3AUTH_NETWORK=sapphire_devnet

# === INTEGRA CONTRACTS (filled after deployment) ===
NEXT_PUBLIC_ASSET_PASSPORT_ADDRESS=
NEXT_PUBLIC_IRWA_TOKEN_ADDRESS=
NEXT_PUBLIC_GOB_ADDRESS=
NEXT_PUBLIC_AGENT_AUTH_ADDRESS=

# === INTEGRA SERVICES ===
NEXT_PUBLIC_FAUCET_URL=https://faucet.integralayer.com
NEXT_PUBLIC_XP_API_URL=https://api.integralayer.com/xp
```

## File-by-File Change Inventory

This is the complete list of files that need modification, with exact changes needed.

### New Files (3)

| File | Content Source |
|------|---------------|
| knowledge/networks/mainnet.md | REQUIREMENTS.md NET-01 through NET-12, STACK.md chain table |
| knowledge/networks/testnet.md | Same structure as mainnet, testnet-specific values |
| knowledge/networks/shared.md | NET-04, NET-07, NET-08, NET-09, CONFIG-10 |

### Updated Files (19)

| File | What Changes | Lines Affected |
|------|-------------|----------------|
| CLAUDE.md (root) | Strip chain section (lines 7-13), strip infrastructure table rows for network data, add pointer to knowledge/networks/, fix faucet amount | ~20 lines |
| .claude/CLAUDE.md | Replace entire design system section (purple->coral, Inter->Euclid), strip network data, sync with root | ~60 lines |
| knowledge/integra-ecosystem.md | Remove chain config table (lines 6-16), remove hardhat config code (lines 19-37), remove wagmi chain code (lines 39-57), fix Web3Auth config code (lines 857-889) | ~80 lines removed |
| knowledge/product-ideas.md | Fix faucet amount from 1000 IRL to 10 IRL | 1 line |
| agents/architect.md | Remove inline RPC/explorer/chainID (lines 42-47), add knowledge/networks/ pointer, update font/version refs | ~15 lines |
| agents/deployer.md | Remove inline network config block (lines 36-61), add dual-network support, fix explorer URLs | ~30 lines |
| agents/executor.md | Remove inline Web3Auth config (lines 166-186), remove inline chain config (lines 188-209), fix package versions, fix design refs | ~50 lines |
| agents/frontend-designer.md | Replace entire design system section (lines 23-50) with correct Integra colors + Euclid font | ~30 lines |
| agents/integration-planner.md | Remove inline network config (lines 24-31), fix block time from 2s to 5s | ~10 lines |
| agents/reviewer.md | Fix design system checklist (lines 72-77): colors, font, backgrounds. Fix chain ID ref (line 86) | ~10 lines |
| templates/hardhat.config.ts.template | Add mainnet network, fix testnet chain ID, update RPC URLs, add gasPrice for mainnet, fix explorer URLs | ~20 lines |
| templates/env.example.template | Add dual-network sections, fix URLs, add Web3Auth network var | Complete rewrite |
| templates/web3auth-provider.tsx.template | Make network-aware (conditional sapphire), fix chain config, fix theme color from #6C5CE7 to #FF6D49 | ~15 lines |
| templates/tailwind-integra.ts.template | NO CHANGE NEEDED -- already correct | 0 lines |
| skills/start/SKILL.md | Fix chain ID reference at line 158 | 1 line |
| skills/deploy/SKILL.md | Support dual-network throughout, fix URLs, fix chain IDs | ~20 lines |
| skills/review/SKILL.md | Fix chain ID reference at line 91 | 1 line |
| skills/explore/SKILL.md | Verify faucet info | ~2 lines |

**Total: 3 new files + 18 updated files (1 template unchanged)**

## Validation Architecture

### Test Framework
| Property | Value |
|----------|-------|
| Framework | Manual grep-based verification (no test runner -- this is a content-only phase) |
| Config file | none |
| Quick run command | `grep -r "testnet-rpc.integralayer.com" --include="*.md" --include="*.template" --include="*.tsx"` |
| Full suite command | Run all 6 grep checks below |

### Phase Requirements to Test Map
| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| NET-01 | Both chain IDs documented | grep | `grep -r "26217" knowledge/networks/mainnet.md && grep -r "26218" knowledge/networks/testnet.md` | Wave 0 |
| NET-03 | Network files exist | file check | `test -f knowledge/networks/mainnet.md && test -f knowledge/networks/testnet.md && test -f knowledge/networks/shared.md` | Wave 0 |
| NET-06 | Faucet amount correct | grep | `grep -r "1000 IRL" . --include="*.md" \| grep -v node_modules \| grep -v .planning/research` (should return 0 hits) | Wave 0 |
| NET-14 | Old RPC URL removed | grep | `grep -r "testnet-rpc.integralayer.com" . --include="*.md" --include="*.template" --include="*.tsx" \| grep -v .planning/research \| grep -v knowledge/networks/` (should return 0 hits) | Wave 0 |
| CONFIG-02 | .claude/CLAUDE.md design system matches root | grep | `grep "#6C5CE7" .claude/CLAUDE.md` (should return 0 hits) | Wave 0 |
| CONFIG-08 | Agents reference knowledge/networks/ | grep | `grep -l "knowledge/networks" agents/*.md \| wc -l` (should be 6+) | Wave 0 |

### Sampling Rate
- **Per task commit:** Run quick grep for deprecated values
- **Per wave merge:** Run all 6 grep checks
- **Phase gate:** All 6 greps clean + manual review of new knowledge files

### Wave 0 Gaps
- None -- no test framework needed. Grep-based validation is sufficient for a content refactoring phase.

## Security Domain

### Applicable ASVS Categories

| ASVS Category | Applies | Standard Control |
|---------------|---------|-----------------|
| V2 Authentication | No | N/A -- no auth changes in this phase |
| V3 Session Management | No | N/A |
| V4 Access Control | No | N/A |
| V5 Input Validation | No | N/A -- content files only |
| V6 Cryptography | No | N/A |

This phase is purely documentation/configuration refactoring. No code is executed, no contracts are deployed, no user-facing features change. Security relevance is indirect: correct network configuration prevents the deployment pitfalls documented in PITFALLS.md (wrong network deployment, Web3Auth mismatch).

### Known Threat Patterns

| Pattern | STRIDE | Standard Mitigation |
|---------|--------|---------------------|
| Wrong network deployment (mainnet vs testnet) | Information Disclosure | Pre-deployment chain ID validation in generated configs (NET-01) |
| Web3Auth network mismatch | Information Disclosure | Derive sapphire_* from chain network config (NET-13) |
| Incorrect gas price on mainnet | Denial of Service | Hardcode min gas price in mainnet config (NET-12) |

## Assumptions Log

| # | Claim | Section | Risk if Wrong |
|---|-------|---------|---------------|
| A1 | Precompile addresses are 0x800-0x808 sequentially | Code Examples (shared.md) | Wrong addresses in generated contracts; precompile calls fail |
| A2 | Correct RPC URL format is `{network}.integralayer.com/evm` | Contradiction Inventory | All generated configs would have wrong RPC URLs |
| A3 | Faucet dispenses 10 IRL + 1,000 tUSDI (from REQUIREMENTS.md) | Contradiction Inventory | Wrong faucet docs, but low impact (testnet only) |
| A4 | Explorer URLs are blockscout.integralayer.com (EVM) and scan.integralayer.com (Cosmos) | Contradiction Inventory | Explorer links in generated dApps would be broken |
| A5 | WebSocket URL format is `wss://{network}.integralayer.com/evm/ws` | Code Examples (mainnet.md) | WebSocket connections would fail if format differs |
| A6 | gRPC endpoint is `{network}.integralayer.com:9090` | Code Examples (mainnet.md) | gRPC connections would fail |
| A7 | Cosmos REST API is at `{network}.integralayer.com/api` | Code Examples (mainnet.md) | Cosmos REST queries would fail |

**Note:** A1-A7 come from the REQUIREMENTS.md and ARCHITECTURE.md research documents which cite the official Integra documentation. They were not independently verified against live endpoints in this session. The planner should ensure the executor verifies endpoint accessibility during implementation if possible.

## Open Questions

1. **Precompile contract addresses**
   - What we know: NET-08 requires documenting 9 precompiles at 0x800-0x808
   - What's unclear: The exact mapping of which precompile is at which address (the ARCHITECTURE.md example shows partial data)
   - Recommendation: Use the addresses from STACK.md research; flag for user verification before finalizing shared.md

2. **Explorer URL per-network differentiation**
   - What we know: NET-11 says blockscout (EVM) and scan (Cosmos)
   - What's unclear: Are these the same URL for both mainnet and testnet, or do they have network-specific subdomains?
   - Recommendation: Use same base URLs for both (the explorer auto-detects network by chain ID); confirm with user

3. **Testnet-specific endpoints beyond EVM RPC**
   - What we know: EVM RPC is testnet.integralayer.com/evm
   - What's unclear: Whether WebSocket, Cosmos RPC, REST, gRPC follow the same pattern for testnet
   - Recommendation: Assume same pattern (testnet.integralayer.com/{path}); confirm with user

## Environment Availability

Step 2.6: SKIPPED (no external dependencies). This phase is purely content/configuration changes to markdown files and templates. No tools, runtimes, databases, or external services are required.

## Sources

### Primary (HIGH confidence)
- REQUIREMENTS.md -- requirement definitions for NET-01 through NET-14, CONFIG-01 through CONFIG-10
- ARCHITECTURE.md (planning research) -- Section 1 network config architecture pattern
- PITFALLS.md (planning research) -- Pitfalls 1-3 (decimal, network swap, Web3Auth mismatch)
- STACK.md (planning research) -- Chain infrastructure table with verified network data
- All files in the repository (agents/, templates/, skills/, knowledge/, CLAUDE.md, .claude/CLAUDE.md) -- directly audited

### Secondary (MEDIUM confidence)
- PROJECT.md -- original requirements capture from user discussion

### Tertiary (LOW confidence)
- Precompile addresses (A1), endpoint URL formats (A2, A5-A7) -- derived from research docs that cite official Integra docs, but not independently verified against live endpoints

## Metadata

**Confidence breakdown:**
- Contradiction inventory: HIGH -- every contradiction was found by directly grepping/reading the actual files
- Architecture patterns: HIGH -- directly taken from the already-approved ARCHITECTURE.md research
- File change inventory: HIGH -- based on complete audit of all 22+ files
- Network data values: MEDIUM -- from REQUIREMENTS.md/STACK.md research, not independently verified against live chain

**Research date:** 2026-04-09
**Valid until:** 2026-05-09 (stable -- content refactoring, no dependency version risk)
