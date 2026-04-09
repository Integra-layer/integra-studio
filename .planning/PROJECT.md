# Integra Studio V2

## What This Is

A Claude Code plugin that guides developers — from zero blockchain experience to deployed dApp — on the Integra blockchain. V2 is a major overhaul: dual-network support (mainnet + testnet), AI-powered UI generation via Google Stitch, a premium design pipeline with 6 UI/UX skills, and a fully selectable wizard experience with no open-ended questions.

## Core Value

Every developer interaction is a selectable choice that produces a production-quality dApp with correct chain configuration, beautiful UI, and full Integra ecosystem integration — no blockchain expertise required.

## Requirements

### Validated

- ✓ Interactive wizard for dApp creation — existing (`/start`)
- ✓ Phase-by-phase build workflow — existing (`/build`)
- ✓ Security + quality review — existing (`/review`)
- ✓ Testnet deployment pipeline — existing (`/deploy`)
- ✓ Brainstorm and research skills — existing
- ✓ 8 specialized agents (discovery, architect, contract-designer, frontend-designer, integration-planner, executor, reviewer, deployer) — existing
- ✓ Web3Auth social login integration — existing
- ✓ XP system event emission — existing
- ✓ Integra design system (dark theme, brand colors) — existing

### Active

- [ ] **NET-01**: Dual-network support — mainnet (Chain ID 26217, 0x6669) and testnet (Chain ID 26218, 0x666A)
- [ ] **NET-02**: Network selector in wizard — default mainnet, user picks testnet/mainnet
- [ ] **NET-03**: Full endpoint config per network — EVM JSON-RPC, WebSocket, Cosmos RPC, REST API, gRPC
- [ ] **NET-04**: Mainnet RPC: `https://mainnet.integralayer.com/evm`, Testnet RPC: `https://testnet.integralayer.com/evm`
- [ ] **NET-05**: Correct faucet info — 10 IRL per request (not 1000), plus 1,000 tUSDI per request
- [ ] **NET-06**: WIRL (Wrapped IRL) token address: `0x5002000000000000000000000000000000000001`
- [ ] **NET-07**: Precompiled contract addresses (staking, distribution, bank, gov, etc.)
- [ ] **NET-08**: Cosmos chain IDs — mainnet: `integra_26217-1`, testnet: `integra-testnet-1`
- [ ] **NET-09**: Dual decimal precision warning — EVM 18 decimals vs Cosmos SDK Bank 6 decimals
- [ ] **STITCH-01**: Google Stitch MCP integration for AI-powered UI screen generation
- [ ] **STITCH-02**: Stitch SDK (`@google/stitch-sdk`) available in generated projects
- [ ] **STITCH-03**: Screen generation from prompts, image-to-UI, variant creation
- [ ] **STITCH-04**: Design system application across generated screens
- [ ] **STITCH-05**: HTML export from Stitch for integration into Next.js projects
- [ ] **UISKILL-01**: Wire ui-ux-pro-max skill into frontend design phase
- [ ] **UISKILL-02**: Wire frontend-design patterns into component architecture
- [ ] **UISKILL-03**: Wire taste-skill for premium visual feel (spacing, hierarchy, polish)
- [ ] **UISKILL-04**: Wire shadcn-UI skill for consistent component usage
- [ ] **UISKILL-05**: Wire UI-animation skill for smooth transitions and motion
- [ ] **UISKILL-06**: Wire web-design-guidelines for layout, typography, responsiveness
- [ ] **UISKILL-07**: Skill pipeline order: guidelines > pro-max > taste > shadcn > animation > frontend-design
- [ ] **WIZ-01**: All wizard questions use AskUserQuestion with selectable options only
- [ ] **WIZ-02**: Free-text input always as last option ("Let me describe..." / "Other")
- [ ] **WIZ-03**: No open-ended questions — every question has concrete selectable choices
- [ ] **WIZ-04**: Network selection step in `/start` wizard — "Build for mainnet or testnet?"
- [ ] **WIZ-05**: Branding toggle — "Use Integra official branding or custom design?"
- [ ] **WIZ-06**: UI generation method — "Generate UI with Stitch AI or build manually?"
- [ ] **BRAND-01**: Integra branding option loads official design system from integra-brand repo
- [ ] **BRAND-02**: Custom design option uses ui-ux-pro-max to generate unique design system
- [ ] **BRAND-03**: Update design-system.md with latest brand assets (Coral #FF6D49 primary, Euclid Circular B font)
- [ ] **CONFIG-01**: Update CLAUDE.md with correct mainnet + testnet dual config
- [ ] **CONFIG-02**: Update knowledge/integra-ecosystem.md with latest chain data
- [ ] **CONFIG-03**: Update templates (hardhat.config, env.example, web3auth-provider) for dual-network
- [ ] **CONFIG-04**: Explorer URLs — blockscout.integralayer.com (EVM), scan.integralayer.com (Cosmos)
- [ ] **CONFIG-05**: Block time ~5s, instant finality (CometBFT), DPoS consensus
- [ ] **CONFIG-06**: Min gas price mainnet: 5,000,000,000,000 airl (5 Twei)

### Out of Scope

- Light theme support — Dark theme only per brand guidelines
- Mobile app generation — Web-first, Next.js only
- Cosmos SDK native module development — EVM smart contracts only
- Multi-chain deployment — Integra network only
- Google Stitch paid features — Free tier only (no caps currently)
- Real-time Stitch preview server — Export HTML only, no live preview

## Context

- Integra is a Cosmos SDK + EVM dual-stack chain with CometBFT consensus
- Mainnet launched with 4 validators (Paris, New York, London, Hong Kong), ~690M IRL each
- Testnet has 3 validators (SantaClara, Helsinki, Amsterdam), ~200M IRL each
- Software: CometBFT v0.38.19, Cosmos SDK v0.53.5, go-ethereum v1.15.11
- EVM state stored in Cosmos IAVL trees (not Merkle Patricia Trie) — affects proof generation
- 3% fixed inflation on mainnet
- Google Stitch (from Galileo AI acquisition) launched at I/O 2025, free with Google account
- Stitch MCP server available via `@_davideast/stitch-mcp` package
- UI/UX skills: ui-ux-pro-max installed (50+ styles, 161 palettes), others need referencing
- Current CLAUDE.md has outdated config (testnet-only, wrong faucet amounts, old RPC format)

## Constraints

- **Chain Config**: Must match live network state — verified against docs.integralayer.com April 2026
- **Web3Auth**: Must use shared provider pattern (`@integra/web3auth-provider`), never raw SDK
- **Design System**: Euclid Circular B font mandatory for Integra branding; Inter/Montserrat/Space Grotesk forbidden
- **Wizard UX**: Every question must use AskUserQuestion tool with 2-4 selectable options
- **Backward Compatibility**: Existing skills must continue to work; V2 extends, doesn't break
- **Google Stitch**: Requires Google account + `STITCH_API_KEY` env var for SDK usage

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Default to mainnet | Mainnet is production-ready; testnet for development/testing only | -- Pending |
| Google Stitch for UI generation | Free, high-quality, MCP-compatible, generates HTML exportable to Next.js | -- Pending |
| Selectable-only wizard | Reduces friction, prevents analysis paralysis, ensures consistent UX | -- Pending |
| 6-skill UI pipeline | Each skill covers a different design dimension; together they produce premium output | -- Pending |
| Dual decimal precision warning | EVM (18) vs Cosmos (6) decimals is a critical footgun for developers | -- Pending |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd-transition`):
1. Requirements invalidated? -> Move to Out of Scope with reason
2. Requirements validated? -> Move to Validated with phase reference
3. New requirements emerged? -> Add to Active
4. Decisions to log? -> Add to Key Decisions
5. "What This Is" still accurate? -> Update if drifted

**After each milestone** (via `/gsd-complete-milestone`):
1. Full review of all sections
2. Core Value check -- still the right priority?
3. Audit Out of Scope -- reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-04-09 after initialization*
