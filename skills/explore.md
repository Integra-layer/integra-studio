---
name: explore
description: Explore the Integra ecosystem — contracts, APIs, features, examples.
user_invocable: true
trigger: "integra-studio:explore"
---

# Explore Skill

Interactive explorer for the Integra ecosystem. Let users discover contracts, APIs, features, and integration patterns.

## Activation

When invoked, show the ecosystem menu:

```
Integra Ecosystem Explorer

Pick a product to explore, or ask a question:

  [1] Asset Passport  — Tokenize and track real-world assets
  [2] Wrapper (IRWA)  — Wrap assets into ERC-20 tokens
  [3] Global Order Book (GOB) — Decentralized OTC marketplace
  [4] Agent Arena     — Build and deploy AI agents on-chain
  [5] XP System       — Gamified participation rewards
  [6] Web3Auth        — Social login + wallet connection
  [7] Faucet          — Get testnet IRL tokens
  [8] Domain Router   — Get an appname.integra.zone subdomain
  [9] IntegraLending  — Lending/borrowing protocol
  [10] PriceOracle    — On-chain price feeds

Or just ask: "How do I mint an asset passport?" / "Show me the Wrapper ABI"
```

## Knowledge Source

Read `knowledge/integra-ecosystem.md` for all contract interfaces, API endpoints, and integration patterns. ALWAYS reference real function signatures from that file — never invent APIs.

## Explorer Behavior for Each Product

### Asset Passport
Show:
- What it is: NFT-based digital twin for real-world assets (ERC-721 extension)
- Key functions: `mintPassport()`, `updateMetadata()`, `transferPassport()`, `getPassportData()`
- Events: `PassportMinted`, `MetadataUpdated`, `PassportTransferred`
- Integration example: How to mint a passport from a frontend using wagmi hooks
- Use cases: Supply chain tracking, luxury goods authentication, real estate deeds

### Wrapper (IRWAToken)
Show:
- What it is: Wrap passported assets into fungible ERC-20 tokens for trading
- Key functions: `wrap()`, `unwrap()`, `totalWrapped()`, `underlyingPassport()`
- Events: `Wrapped`, `Unwrapped`
- Integration example: How to wrap/unwrap from frontend
- Use cases: Fractional ownership, liquidity for illiquid assets, DeFi composability

### Global Order Book (GOB)
Show:
- What it is: On-chain OTC marketplace for any asset pair
- Key functions: `createOrder()`, `fillOrder()`, `cancelOrder()`, `getOrderBook()`
- Events: `OrderCreated`, `OrderFilled`, `OrderCancelled`
- Integration example: How to list and fill orders from frontend
- Use cases: P2P trading, RWA secondary market, bulk deals with negotiation

### Agent Arena
Show:
- What it is: Platform for building, deploying, and monetizing AI agents on Integra
- Key components: AgentAuth (on-chain agent identity), Agent Registry, Execution Layer
- Key functions: `registerAgent()`, `executeTask()`, `getAgentProfile()`
- Events: `AgentRegistered`, `TaskExecuted`, `RewardDistributed`
- Integration example: How to register an agent and handle task execution
- Use cases: Autonomous trading agents, data analysis agents, content generation agents

### XP System
Show:
- What it is: Cross-dApp gamification layer that rewards user participation
- Key functions: `emitXP()`, `getUserXP()`, `getLeaderboard()`
- Events: `XPEarned`
- API endpoints: `GET /api/xp/{address}`, `GET /api/leaderboard`
- Integration example: How to emit XP events from your dApp and display user XP
- Use cases: User retention, cross-dApp engagement, reputation building
- Important: XP categories (interact, trade, create, governance, social)

### Web3Auth
Show:
- What it is: Social login that creates a wallet behind the scenes
- Setup: Web3Auth SDK configuration, client ID, chain config
- Integration example: Full login flow with Google/Twitter/Discord
- Key patterns: How to get the provider, how to get the user's address, how to sign transactions
- Important: Users don't need MetaMask — they log in with social accounts

### Faucet
Show:
- What it is: Testnet token dispenser for development
- URL: `https://faucet.integralayer.com`
- API: `POST /api/faucet/drip` with `{ address: "0x..." }`
- Limits: Amount per request, cooldown period
- Integration: How to add a "Get Test IRL" button in your dApp

### Domain Router
Show:
- What it is: Subdomain service for Integra dApps
- How to register: `appname.integra.zone`
- Configuration: DNS setup, SSL, deployment target
- Integration: How to configure in your deployment pipeline

### IntegraLending
Show:
- What it is: Lending/borrowing protocol for Integra assets
- Key functions: `deposit()`, `borrow()`, `repay()`, `liquidate()`, `getHealthFactor()`
- Events: `Deposited`, `Borrowed`, `Repaid`, `Liquidated`
- Integration example: How to build a lending frontend
- Use cases: Leverage on RWA-backed tokens, yield on deposits

### PriceOracle
Show:
- What it is: On-chain price feeds for Integra assets
- Key functions: `getPrice()`, `getLatestRound()`, `updatePrice()`
- Events: `PriceUpdated`
- Integration example: How to read prices in contracts and frontend
- Use cases: DeFi pricing, liquidation triggers, portfolio valuation

## Interaction Pattern

After showing information about a product:
1. Show 2-3 related follow-up suggestions ("Want to see how this connects to the Wrapper?" / "See a full integration example?")
2. Offer to show code: "Want me to generate a starter hook for this?"
3. Allow the user to switch to another product or ask a free-form question
4. If the user seems ready to build, offer: "Ready to build something with this? Try `/integra-studio:start` or `/integra-studio:brainstorm`"

## Cross-Product Patterns

When relevant, show how products connect:
- Asset Passport + Wrapper = tokenize then make tradeable
- Wrapper + GOB = create tokens then list on marketplace
- Any product + XP = gamify interactions
- Any product + Web3Auth = onboard users without MetaMask
- Any product + PriceOracle = real-time valuation
- Any product + IntegraLending = create DeFi composability
