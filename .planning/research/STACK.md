# Technology Stack

**Project:** Integra Studio V2
**Researched:** 2026-04-09

## Stack Overview

Integra Studio V2 operates at three levels: (1) the Claude Code plugin itself, (2) the dApp projects it generates, and (3) the chain infrastructure those dApps target. This document covers all three.

---

## Level 1: Plugin Internals (What the Studio Uses)

These are tools the Studio plugin consumes directly during the wizard and build workflows.

### Google Stitch Integration

| Technology | Version | Purpose | Why |
|------------|---------|---------|-----|
| `@google/stitch-sdk` | 0.0.3 | Programmatic UI screen generation from text prompts | Official Google SDK, generates HTML/CSS screens from natural language, free tier with no caps |
| `@_davideast/stitch-mcp` | latest | MCP server bridge for Stitch in Claude Code | Enables Claude Code to call Stitch tools (build_site, get_screen_code, get_screen_image) via MCP protocol |

**Stitch SDK details:**
- Install: `npm install @google/stitch-sdk`
- Auth: OAuth via gcloud (tokens expire hourly) or `STITCH_API_KEY` env var
- MCP init: `npx @_davideast/stitch-mcp init`
- Supported clients: VS Code, Cursor, Claude Code, Gemini CLI
- Key methods: `stitch.project(id)`, `project.generateScreen(prompt)`, screen.html, screen.imageUrl
- StitchToolClient for explicit config with custom API keys
- Integrates with Vercel AI SDK via `stitchTools()`

**Confidence:** MEDIUM - SDK is v0.0.3 (pre-1.0), API surface may change. MCP server is community-maintained by David East (Google DevRel). Verify latest API before generating code.

### UI/UX Skill Pipeline

| Skill | Order | Purpose |
|-------|-------|---------|
| web-design-guidelines | 1 | Layout, typography, responsiveness foundations |
| ui-ux-pro-max | 2 | 50+ styles, 161 palettes, premium design patterns |
| taste-skill | 3 | Spacing, hierarchy, visual polish |
| shadcn-UI | 4 | Consistent component usage with shadcn patterns |
| UI-animation | 5 | Smooth transitions and motion design |
| frontend-design | 6 | Component architecture and composition |

**Note:** These are Claude Code skills (prompt injections), not npm packages. They shape how the executor agent writes frontend code.

### AskUserQuestion

The wizard's interactive prompt tool. All questions must use selectable options (2-4 choices), with free-text as a last resort option. This is a Claude Code built-in, not a dependency.

---

## Level 2: Generated dApp Stack (What Projects Use)

This is the stack that generated dApp projects will include.

### Core Framework

| Technology | Version | Purpose | Why |
|------------|---------|---------|-----|
| Next.js | 16.x (latest 16.2.3) | Full-stack React framework | App Router is stable and mature, React 19.2 support, React Compiler stable, 400% faster dev startup. Use App Router exclusively (Pages Router is maintenance-only). |
| React | 19.2.x | UI library | Ships with Next.js 16, React Compiler 1.0 auto-memoizes components |
| TypeScript | 5.x (strict mode) | Type safety | Required by project conventions, catches contract interaction bugs early |

**Next.js 16 highlights:**
- React Compiler 1.0 built-in (auto-memoization, zero manual code changes)
- Layout deduplication and incremental prefetching
- ~50% faster rendering vs v15
- Redesigned error pages

### Blockchain Interaction

| Technology | Version | Purpose | Why |
|------------|---------|---------|-----|
| wagmi | 3.x (latest 3.6.0) | React hooks for Ethereum | v3 is current stable. Renamed useAccount to useConnection, better TanStack Query alignment. Light wrapper around viem. |
| viem | 2.x (latest 2.47.10) | TypeScript Ethereum client | Powers wagmi under the hood, type-safe contract interactions, EIP-1193 compatible |
| @tanstack/react-query | 5.x | Async state management | Required peer dependency for wagmi v3, handles caching/refetching of chain data |
| ethers.js | 6.x (latest 6.16.0) | Ethereum utilities (Hardhat only) | Used in Hardhat test scripts and deployment. Frontend uses viem via wagmi instead. |

**wagmi v3 migration note:** v2 to v3 is relatively smooth -- mostly renames (useAccount -> useConnection, useSwitchAccount -> useSwitchConnection). Old v2 patterns are deprecated but still functional initially.

**Decision: wagmi v3 + viem v2** because wagmi v3 is the current stable release and the migration from v2 is incremental. The CLAUDE.md currently says "wagmi v2" -- this needs updating.

### Wallet Authentication

| Technology | Version | Purpose | Why |
|------------|---------|---------|-----|
| @web3auth/modal | 10.x (latest 10.15.0) | Social login modal (Google, X, Email) | v10 unified modal and no-modal SDKs, simplified chain config via Dashboard |
| @web3auth/ethereum-provider | 10.x | EVM EIP-1193 provider | Provides standard provider after Web3Auth authentication, works with viem/wagmi |

**Web3Auth v10 changes:**
- Chain configuration moved to Dashboard (less code-side setup)
- Unified package (modal + no-modal in one)
- chainNamespace: `eip155` for EVM chains
- Provider is EIP-1193 compatible, works directly with viem/wagmi

**Important constraint:** Per project conventions, dApps must use `@integra/web3auth-provider` (shared wrapper package), never raw Web3Auth SDK directly. The wrapper handles Integra-specific config (chain IDs, RPC endpoints, sapphire_devnet network).

### UI Components

| Technology | Version | Purpose | Why |
|------------|---------|---------|-----|
| Tailwind CSS | 4.x (latest 4.2.0) | Utility-first CSS | v4 has CSS-first config (no tailwind.config.js), massive perf improvements, new logical property utilities |
| shadcn/ui | CLI v4 (latest) | Copy-paste component library | Full code ownership, zero runtime overhead, 20-50KB total. Three-tier org: ui/ -> primitives/ -> blocks/ |
| Radix UI | unified package | Accessible primitives | Powers shadcn/ui under the hood, now unified into single package |
| React Hook Form | 7.x | Form state management | Direct integration with shadcn (no wrapper component), pairs with Zod validation |
| Zod | 3.x | Schema validation | Type-safe validation, generates TypeScript types, zodResolver for React Hook Form |

**Tailwind CSS v4 migration notes:**
- Config is now CSS-first (`@theme` directive in CSS), not `tailwind.config.js`
- New webpack plugin: `@tailwindcss/webpack`
- New logical property utilities (pbs-*, pbe-*, mbs-*, mbe-*)
- Breaking change from v3: config format completely different

**shadcn/ui 2026 changes:**
- CLI v4 released March 2026, built for coding agents
- RTL support added
- Radix UI unified into single package
- shadcn/skills provides agent-friendly component context
- Forms moved to direct RHF/TanStack Form integration (no wrapper <Form> component)

### Smart Contract Development

| Technology | Version | Purpose | Why |
|------------|---------|---------|-----|
| Hardhat | 3.x (latest 3.3.0) | EVM development environment | v3 is production-ready with Rust-based EDR (Ethereum Development Runtime), Foundry-compatible Solidity tests |
| Hardhat Ignition | 3.x | Declarative deployments | Replaces ad-hoc deploy scripts, supports ethers.js integration for tests |
| @nomicfoundation/hardhat-ethers | 3.x | ethers.js plugin for Hardhat | Bridges Hardhat and ethers v6 |
| OpenZeppelin Contracts | 5.x | Audited contract standards | ERC-20/721/1155/3643, ReentrancyGuard, Ownable, AccessControl |
| Solidity | 0.8.24+ | Smart contract language | Per project conventions, NatSpec required on all public functions |

**Hardhat 3 migration from v2:**
- `npx hardhat compile` -> `npx hardhat build`
- `npx hardhat init` -> `npx hardhat --init`
- Plugins must be both imported AND added to plugins array
- Task definition uses setInlineAction + .build()
- Rust-based EDR replaces JS simulation layer (much faster)
- Foundry-compatible Solidity test support (write tests in Solidity)
- Execution tracing now available for JS/TS integration tests

**Decision: Hardhat 3** because it is production-ready (v3.3.0), the Rust EDR is significantly faster, and Foundry-compatible tests add flexibility. The CLAUDE.md currently references Hardhat generically -- should specify v3.

### Supporting Libraries

| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| lucide-react | latest | Icons | Default icon set for shadcn/ui |
| date-fns | latest | Date formatting | When displaying timestamps, block times |
| sonner | latest | Toast notifications | Transaction status, errors, XP earned |
| nuqs | latest | URL search params state | Filter/sort state in GOB listings, portfolio views |

---

## Level 3: Chain Infrastructure (What dApps Target)

### Integra Network

| Property | Mainnet | Testnet |
|----------|---------|---------|
| Chain Name | integra_26217-1 (Cosmos) | integra-testnet-1 (Cosmos) |
| Chain ID (EVM) | 26217 (0x6669) | 26218 (0x666A) |
| EVM RPC | `https://mainnet.integralayer.com/evm` | `https://testnet.integralayer.com/evm` |
| Token | IRL / airl (18 decimals EVM, 6 decimals Cosmos) | IRL / airl |
| Consensus | CometBFT v0.38.19 (DPoS, ~5s blocks, instant finality) | Same |
| SDK | Cosmos SDK v0.53.5 + go-ethereum v1.15.11 | Same |
| Min Gas Price (Mainnet) | 5,000,000,000,000 airl (5 Twei) | -- |
| Explorer (EVM) | blockscout.integralayer.com | blockscout.integralayer.com |
| Explorer (Cosmos) | scan.integralayer.com | scan.integralayer.com |
| Faucet | N/A | faucet.integralayer.com (10 IRL + 1,000 tUSDI per request) |
| WIRL Address | `0x5002000000000000000000000000000000000001` | Same |

**Critical footgun:** EVM uses 18 decimals, Cosmos SDK Bank uses 6 decimals. Generated dApps MUST handle this conversion explicitly or users will lose funds.

**EVM state note:** Stored in Cosmos IAVL trees (not Merkle Patricia Trie). This affects proof generation and any dApp that needs storage proofs.

---

## Alternatives Considered

| Category | Recommended | Alternative | Why Not |
|----------|-------------|-------------|---------|
| Framework | Next.js 16 | Remix, Vite+React | Next.js is the established Integra convention, App Router is mature, SSR for SEO on marketplace pages |
| Contract Framework | Hardhat 3 | Foundry | Hardhat is the established convention, JS/TS ecosystem alignment, Ignition for deployments. But Hardhat 3 now supports Foundry-compatible Solidity tests as a bridge. |
| CSS | Tailwind v4 | CSS Modules, vanilla-extract | Tailwind is established convention, shadcn/ui requires it, v4 is faster |
| Components | shadcn/ui | Chakra UI, Mantine | shadcn/ui gives full code ownership (critical for Integra brand customization), zero runtime cost |
| Wallet | Web3Auth v10 | RainbowKit, ConnectKit | Web3Auth is mandated by Integra -- social login is core UX requirement for non-crypto users |
| Contract Interaction | wagmi v3 + viem | ethers.js directly | wagmi provides React hooks with caching, viem is type-safe, both are lighter than ethers in frontend bundle |
| State Management | React hooks + wagmi + TanStack Query | Redux, Zustand | wagmi + TanStack Query handles all async chain state; local state via React hooks. Add Zustand only if complex cross-component state emerges. |
| UI Generation | Google Stitch | v0.dev, Bolt | Stitch is free, MCP-native, Google-backed. But SDK is v0.0.3 -- have manual fallback ready. |

---

## Installation Templates

### Generated dApp: Frontend

```bash
# Core framework
npx create-next-app@latest my-dapp --typescript --tailwind --app --src-dir

# Blockchain
npm install wagmi viem @tanstack/react-query

# UI components (via shadcn CLI v4)
npx shadcn@latest init
npx shadcn@latest add button card dialog input label select tabs toast

# Forms
npm install react-hook-form @hookform/resolvers zod

# Utilities
npm install lucide-react sonner date-fns nuqs

# Integra-specific (shared packages)
npm install @integra/web3auth-provider
```

### Generated dApp: Contracts

```bash
# Initialize Hardhat 3
npx hardhat --init

# Core dependencies
npm install --save-dev hardhat @nomicfoundation/hardhat-ethers ethers
npm install --save-dev @nomicfoundation/hardhat-ignition

# OpenZeppelin
npm install @openzeppelin/contracts

# Testing
npm install --save-dev @nomicfoundation/hardhat-chai-matchers chai
npm install --save-dev @typechain/hardhat @typechain/ethers-v6 typechain

# Verification
npm install --save-dev @nomicfoundation/hardhat-verify
```

### Studio Plugin: MCP Setup

```bash
# Stitch MCP (for UI generation)
npx @_davideast/stitch-mcp init

# Requires: Google account + STITCH_API_KEY or gcloud OAuth
```

---

## Version Pinning Strategy

For generated dApps, pin major versions to avoid breaking changes:

```json
{
  "next": "^16.2.0",
  "react": "^19.2.0",
  "wagmi": "^3.6.0",
  "viem": "^2.47.0",
  "@tanstack/react-query": "^5.0.0",
  "@web3auth/modal": "^10.15.0",
  "hardhat": "^3.3.0",
  "ethers": "^6.16.0",
  "@openzeppelin/contracts": "^5.0.0",
  "tailwindcss": "^4.2.0",
  "react-hook-form": "^7.0.0",
  "zod": "^3.0.0"
}
```

---

## CLAUDE.md Update Requirements

The current CLAUDE.md has several outdated references that V2 must correct:

| Current | Should Be | Why |
|---------|-----------|-----|
| "wagmi v2" | wagmi v3 | v3 is current stable (3.6.0) |
| "Next.js 14+" | Next.js 16+ | v16 is current stable (16.2.3) |
| No Hardhat version specified | Hardhat 3 | v3 is production-ready (3.3.0), major API changes from v2 |
| No Tailwind version specified | Tailwind CSS 4 | v4 has completely different config format (CSS-first) |
| Testnet-only config | Dual-network (mainnet + testnet) | Per NET-01 through NET-09 requirements |
| "1000 IRL per address per 24h" faucet | "10 IRL + 1,000 tUSDI per request" | Corrected per PROJECT.md |

---

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Next.js 16 | HIGH | Official docs confirm 16.2.3, App Router stable |
| wagmi v3 / viem v2 | HIGH | npm confirms 3.6.0 / 2.47.10, migration guide exists |
| Hardhat 3 | HIGH | npm confirms 3.3.0, official migration docs available |
| Tailwind CSS 4 | HIGH | v4.2.0 confirmed, CSS-first config documented |
| shadcn/ui CLI v4 | HIGH | March 2026 release confirmed, agent-friendly features |
| Web3Auth v10 | HIGH | npm confirms 10.15.0, migration guide v8->v9->v10 exists |
| Google Stitch SDK | MEDIUM | v0.0.3 pre-release, API may change. MCP server is community-maintained. |
| Ethers v6 | HIGH | v6.16.0 confirmed, stable |
| OpenZeppelin v5 | MEDIUM | Not directly verified this session, but v5 is standard for Solidity 0.8.x |
| Chain infrastructure | HIGH | Verified against PROJECT.md which cites April 2026 docs.integralayer.com |

---

## Sources

- [Next.js 16 Blog Post](https://nextjs.org/blog/next-16)
- [Next.js Releases](https://github.com/vercel/next.js/releases)
- [wagmi v2 to v3 Migration](https://wagmi.sh/react/guides/migrate-from-v2-to-v3)
- [wagmi npm](https://www.npmjs.com/package/wagmi)
- [viem npm](https://www.npmjs.com/package/viem)
- [Hardhat 3 What's New](https://hardhat.org/docs/hardhat3/whats-new)
- [Hardhat v2 to v3 Migration](https://hardhat.org/docs/migrate-from-hardhat2)
- [Hardhat npm](https://www.npmjs.com/package/hardhat)
- [Tailwind CSS v4.0 Blog](https://tailwindcss.com/blog/tailwindcss-v4)
- [Tailwind CSS 4.2 InfoQ](https://www.infoq.com/news/2026/04/tailwind-css-4-2-webpack/)
- [shadcn/ui CLI v4 Changelog](https://ui.shadcn.com/docs/changelog/2026-03-cli-v4)
- [shadcn/ui Best Practices 2026](https://medium.com/write-a-catalyst/shadcn-ui-best-practices-for-2026-444efd204f44)
- [Web3Auth Modal npm](https://www.npmjs.com/package/@web3auth/modal)
- [Web3Auth v10 EVM Provider Docs](https://web3auth.io/docs/sdk/pnp/web/providers/evm)
- [Google Stitch SDK GitHub](https://github.com/google-labs-code/stitch-sdk)
- [Stitch MCP GitHub](https://github.com/davideast/stitch-mcp)
- [Stitch MCP npm](https://www.npmjs.com/package/@_davideast/stitch-mcp)
- [ethers.js npm](https://www.npmjs.com/package/ethers)
