# Requirements: Integra Studio V2

**Defined:** 2026-04-09
**Core Value:** Every developer interaction is a selectable choice that produces a production-quality dApp with correct chain configuration, beautiful UI, and full Integra ecosystem integration.

## v1 Requirements

### Network Configuration (NET)

- [ ] **NET-01**: Dual-network support — mainnet (Chain ID 26217/0x6669) and testnet (Chain ID 26218/0x666A)
- [ ] **NET-02**: Network selector in wizard — "Build for mainnet or testnet?" with default mainnet
- [ ] **NET-03**: Separate network knowledge files — `knowledge/networks/mainnet.md` and `knowledge/networks/testnet.md`
- [ ] **NET-04**: Shared network knowledge — `knowledge/networks/shared.md` (IRL/airl, WIRL, decimals, precompiles)
- [ ] **NET-05**: Full endpoint config per network — EVM JSON-RPC, WebSocket, Cosmos RPC, REST API, gRPC
- [ ] **NET-06**: Correct faucet info — 10 IRL + 1,000 tUSDI per request (fix from incorrect 1,000 IRL)
- [ ] **NET-07**: WIRL token address documented: `0x5002000000000000000000000000000000000001`
- [ ] **NET-08**: All 9 precompiled contract addresses (0x800-0x808: staking, distribution, ICS-20, vesting, authz, bank, gov, slashing, evidence)
- [ ] **NET-09**: Dual decimal precision warning — EVM 18 decimals vs Cosmos SDK Bank 6 decimals
- [ ] **NET-10**: Cosmos chain IDs — mainnet: `integra_26217-1`, testnet: `integra-testnet-1`
- [ ] **NET-11**: Explorer URLs — blockscout.integralayer.com (EVM), scan.integralayer.com (Cosmos)
- [ ] **NET-12**: Min gas price mainnet: 5 Twei (5,000,000,000,000 airl)
- [ ] **NET-13**: Web3Auth sapphire_mainnet for mainnet, sapphire_devnet for testnet
- [ ] **NET-14**: Remove old testnet-rpc.integralayer.com URL, use testnet.integralayer.com/evm

### Wizard UX (WIZ)

- [ ] **WIZ-01**: All technical config questions use AskUserQuestion with 2-4 selectable options
- [ ] **WIZ-02**: Free-text always as last option ("Let me describe..." / "Other")
- [ ] **WIZ-03**: No open-ended questions for configuration — only for ideation/discovery phase
- [ ] **WIZ-04**: Network selection step early in `/start` wizard
- [ ] **WIZ-05**: Branding toggle — "Use Integra official branding or custom design?"
- [ ] **WIZ-06**: UI generation method — "Generate UI with Stitch AI or build manually?"
- [ ] **WIZ-07**: Hybrid approach: open-ended for person-first discovery, selectable for all technical decisions
- [ ] **WIZ-08**: Update discovery agent to use selectable options for category/complexity/features

### Google Stitch Integration (STITCH)

- [ ] **STITCH-01**: Document Stitch MCP server setup (`@_davideast/stitch-mcp`) in knowledge file
- [ ] **STITCH-02**: Frontend-designer agent can invoke Stitch to generate screen mockups
- [ ] **STITCH-03**: Stitch design system creation from Integra brand tokens via `createDesignSystem()`
- [ ] **STITCH-04**: Variant generation (1-3 options) for user selection during wizard
- [ ] **STITCH-05**: HTML export from Stitch as visual specification for executor agent
- [ ] **STITCH-06**: Graceful fallback to manual UI pipeline if Stitch unavailable
- [ ] **STITCH-07**: STITCH_API_KEY documented in `.env.example` template

### UI/UX Skill Pipeline (UISKILL)

- [ ] **UISKILL-01**: Wire ui-ux-pro-max into frontend-designer agent for design direction
- [ ] **UISKILL-02**: Reference frontend-patterns skill for component architecture
- [ ] **UISKILL-03**: Reference react-dev skill for React 18-19 patterns
- [ ] **UISKILL-04**: Build phase invokes skill pipeline: guidelines > pro-max > shadcn > animation > frontend-design
- [ ] **UISKILL-05**: Lean pipeline for Integra branding (skip pro-max style generation, use brand tokens)
- [ ] **UISKILL-06**: Full pipeline for custom branding (pro-max generates unique design system)
- [ ] **UISKILL-07**: Executor agent instructions reference all 6 skills by name for consultation

### Branding (BRAND)

- [ ] **BRAND-01**: Integra branding loads official design tokens (Coral #FF6D49, Euclid Circular B)
- [ ] **BRAND-02**: Custom design triggers ui-ux-pro-max to generate unique design system
- [ ] **BRAND-03**: Both paths produce same output format: CSS variables + Tailwind config
- [ ] **BRAND-04**: Update design-system.md with latest brand assets from integra-brand repo
- [ ] **BRAND-05**: Forbidden fonts list enforced: Inter, Montserrat, Space Grotesk, Kode Mono
- [ ] **BRAND-06**: Forbidden colors list enforced in Integra branding mode

### Config Updates (CONFIG)

- [ ] **CONFIG-01**: Refactor CLAUDE.md — move network data out, keep behavior rules only
- [ ] **CONFIG-02**: Update .claude/CLAUDE.md to match root CLAUDE.md
- [ ] **CONFIG-03**: Update knowledge/integra-ecosystem.md — remove network config, keep products/APIs
- [ ] **CONFIG-04**: Update templates/hardhat.config.ts.template for dual-network
- [ ] **CONFIG-05**: Update templates/env.example.template with mainnet + testnet vars
- [ ] **CONFIG-06**: Update templates/web3auth-provider.tsx.template for network-aware sapphire config
- [ ] **CONFIG-07**: Update templates/tailwind-integra.ts.template with latest brand tokens
- [ ] **CONFIG-08**: Update agents to read from knowledge/networks/ instead of CLAUDE.md inline config
- [ ] **CONFIG-09**: Deploy skill supports both mainnet and testnet deployment targets
- [ ] **CONFIG-10**: Consensus/block info: ~5s blocks, instant finality (CometBFT DPoS)

### Documentation (DOC)

- [ ] **DOC-01**: Update README.md to reflect V2 capabilities
- [ ] **DOC-02**: Knowledge file for Google Stitch integration guide
- [ ] **DOC-03**: Knowledge file for UI/UX skill pipeline guide

## v2 Requirements (Deferred)

### Extension System
- **EXT-01**: Composable integration packs (GOB trading, lending, Agent Arena)
- **EXT-02**: `npx integra-studio add [pack]` style CLI extension

### Advanced Stitch
- **STITCH-08**: Stitch SDK in generated projects for runtime UI generation
- **STITCH-09**: Live Stitch preview server integration

### Local Development
- **LOCAL-01**: Hardhat local node auto-setup with Integra precompile mocks
- **LOCAL-02**: Local faucet script for dev environment

## Out of Scope

| Feature | Reason |
|---------|--------|
| Light theme | Brand guidelines: dark theme default. Custom branding can include light if user wants. |
| Multi-chain deployment | Integra-only. Single chain, dual network. |
| Cosmos SDK module development | EVM contracts only via Hardhat + Solidity |
| Mobile app generation | Web-first, Next.js only |
| Paid Stitch features | Free tier only — no dependency on paid APIs |
| Redux/Zustand by default | React hooks + wagmi sufficient for 90% of dApps |
| Live Stitch preview server | Export HTML only — use `npm run dev` for preview |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| NET-01 to NET-14 | Phase 1 | Pending |
| CONFIG-01 to CONFIG-10 | Phase 1 | Pending |
| WIZ-01 to WIZ-08 | Phase 2 | Pending |
| BRAND-01 to BRAND-06 | Phase 2 | Pending |
| STITCH-01 to STITCH-07 | Phase 3 | Pending |
| UISKILL-01 to UISKILL-07 | Phase 4 | Pending |
| DOC-01 to DOC-03 | Phase 5 | Pending |

**Coverage:**
- v1 requirements: 52 total
- Mapped to phases: 52
- Unmapped: 0

---
*Requirements defined: 2026-04-09*
*Last updated: 2026-04-09 after research synthesis*
