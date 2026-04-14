<div align="center">

<br />

<img src="https://raw.githubusercontent.com/Integra-layer/integra-brand/main/public/logos/integra-mark.svg" alt="Integra" width="64" />

<br />

# Integra Developer Studio

**Build anything on Integra. Start from wherever you are.**

A Claude Code plugin that talks to you, understands what you care about,
and helps you design, build, and deploy a dApp on the Integra blockchain.

<br />

[![Claude Code](https://img.shields.io/badge/Claude_Code-Plugin-FF6D49?style=flat-square)](https://claude.ai/claude-code)
[![Brand](https://img.shields.io/badge/Brand-integra--brand-F34499?style=flat-square)](https://github.com/Integra-layer/integra-brand)
[![Mainnet](https://img.shields.io/badge/Mainnet-26217-00A186?style=flat-square)]()
[![Testnet](https://img.shields.io/badge/Testnet-26218-FFC17A?style=flat-square)]()

</div>

<br />

## Get started

```bash
claude --plugin-dir /path/to/integra-studio
```

Then:

```
/integra-studio:start
```

That's it. The rest is a conversation.

---

## What's new in V3

| Feature | What changed |
|---------|-------------|
| **Multi-token ecosystem** | dApps now support IRL (native gas) + tUSDI (ERC-20 stablecoin, `0xa640...04f`) + WIRL (wrapped IRL). The wizard auto-selects which tokens matter based on your dApp type — DeFi gets IRL/tUSDI trading pairs, gaming gets tUSDI prize pools, social gets tUSDI tipping. All 6 templates include token integration patterns and Solidity snippets. |
| **Best practices library** | 7 guides in `knowledge/best-practices/` that every skill now reads during build and review. Covers UI/UX component patterns, frontend performance (Core Web Vitals targets, bundle budgets), WCAG AA accessibility (contrast ratios, keyboard nav, ARIA), frontend security (XSS, Stitch safety, transaction confirmation), onboarding UX (zero-balance flows, Web3 error mapping), and a master quality checklist with A-F project grading. |
| **Design adaptation by category** | Each dApp category gets design guidance rooted in color psychology. DeFi → cool tones + data-dense dashboards (trust). Gaming → vibrant accents + energetic animations (excitement). NFT → neutral surfaces + generous whitespace (gallery). Social → warm tones + feed layouts (community). AI Agents → dark surfaces + monospace data (futuristic). Includes Integra brand adaptation notes per category. |
| **Enhanced `/review`** | The review skill now runs 6 checks instead of 4: contract security, frontend quality, Integra compliance, **token integration** (correct addresses, configurable not hardcoded), **UI/UX quality** (accessibility, design-category fit, onboarding), and **performance** (bundle size, LCP, CLS). Report format includes token and UI/UX scoring. |
| **Smarter `/start` wizard** | Blueprint now includes token selection rationale and design adaptation notes. Project config (`config.json`) stores `category` and `tokens` fields so downstream skills know which tokens and design mood to apply. |

### Previous (V2)

| Feature | What changed |
|---------|-------------|
| **Dual network** | Mainnet (26217) and testnet (26218) both supported. Wizard asks which one. |
| **Stitch AI** | Google Stitch generates UI screens from prompts. You pick a style variant, executor rebuilds in React. |
| **Branding toggle** | Official Integra brand or custom design. Custom uses ui-ux-pro-max to generate a unique palette. |
| **UI skill pipeline** | 7 skills (guidelines, pro-max, taste, shadcn, animation, patterns, react-dev) polish every frontend. |
| **Selectable wizard** | Every technical question has 2-4 selectable options. No more open-ended config questions. |

---

## What happens

```
 You                          Studio
  |                             |
  |  "Tell me about yourself"   |
  |<----------------------------|
  |                             |
  |  (your background,          |
  |   interests, problems)      |
  |--- ----------------------->|
  |                             |
  |  Connects your world to     |
  |  what's possible on Integra |
  |<----------------------------|
  |                             |
  |  You shape the idea         |
  |  together                   |
  |<- ------------------------>|
  |                             |
  |  "Mainnet or testnet?"      |
  |  "Integra brand or custom?" |
  |  "Stitch AI or manual UI?"  |
  |<----------------------------|
  |                             |
  |         +-------------+     |
  |         | 4 agents    |     |
  |         | design it   |     |
  |         | in parallel |     |
  |         +------+------+     |
  |                |            |
  |  "Does this look right?"    |
  |<----------------------------|
  |                             |
  |  /integra-studio:build      |
  |  (phase by phase,           |
  |   you approve each one)     |
  |<- ------------------------>|
  |                             |
  |  yourapp.integralayer.com   |
  |<----------------------------|
```

The wizard doesn't pick for you. It listens, asks follow-ups based on who you are, and helps you find an idea that only you would come up with.

---

## Commands

| Command | When to use it |
|---------|---------------|
| `/integra-studio:start` | You want to build something (or figure out what to build) |
| `/integra-studio:brainstorm` | You just want to explore ideas, no commitment |
| `/integra-studio:research` | Investigate patterns, security, or pitfalls before building |
| `/integra-studio:build` | Your project is scaffolded, time to write code |
| `/integra-studio:explore` | You want to learn about an Integra feature |
| `/integra-studio:review` | Check your code for security and quality |
| `/integra-studio:deploy` | Ship to testnet or mainnet |
| `/integra-studio:status` | See where your project stands |

---

## How it's built

### Agents

Eight agents handle different parts of the work:

```
discovery          Understands who you are and what you want
architect          Designs the system -- files, stack, data flow
contract-designer  Writes Solidity interfaces and specs
frontend-designer  Designs pages, components, user flows + Stitch AI screens
integration-planner Maps connections to the Integra ecosystem
executor           Writes the actual code (7-skill UI pipeline)
reviewer           Checks security, quality, brand compliance
deployer           Ships to testnet/mainnet and configures subdomain
```

They run in parallel when possible.

### Knowledge

The studio knows the Integra ecosystem:

```
Networks       Mainnet (26217) + Testnet (26218) -- dual config
Tokens         IRL (native) + tUSDI (stablecoin) + WIRL (wrapped) -- per-category selection
Products       Asset Passport + Wrapper + GOB + Agent Arena
Infra          Web3Auth + AgentAuth + XP System + Faucet
Standards      ERC-7943 + ERC-3643 + ERC-8004 + ERC-6551
Design         Integra brand tokens (Coral, Euclid Circular B)
Stitch         Google Stitch MCP -- AI screen generation
UI Pipeline    7 skills for premium frontend quality
Best Practices 7 guides -- UI/UX, performance, a11y, security, onboarding, quality, design adaptation
```

Plus the full [Integra brand system](https://github.com/Integra-layer/integra-brand) -- colors, fonts, gradients, components.

### UI Generation

Two paths, same quality:

**With Stitch AI** (requires `STITCH_API_KEY`):
1. Frontend-designer generates screens via Stitch MCP
2. You pick from 1-3 style variants per key screen
3. Executor rebuilds in Next.js/React using the HTML as visual spec
4. UI skill pipeline polishes every component

**Without Stitch** (manual pipeline):
1. Frontend-designer produces component trees and wireframe descriptions
2. Executor builds from the design document
3. UI skill pipeline polishes every component

Both paths produce the same result -- Stitch just gives you visual options to choose from.

### Templates

When it scaffolds a project, it follows Integra conventions:

- **Contracts** -- Solidity 0.8.24+, Hardhat, OpenZeppelin
- **Frontend** -- Next.js 14+, TypeScript, Tailwind, shadcn/ui
- **Wallet** -- Web3Auth (Google, X, Email login)
- **Design** -- [integra-brand](https://github.com/Integra-layer/integra-brand) tokens or custom (ui-ux-pro-max)
- **Deployment** -- `yourapp.integralayer.com` via Caddy

---

## Project structure

```
integra-studio/
|-- CLAUDE.md                          Main instructions
|-- agents/                            8 agent definitions
|   |-- discovery.md
|   |-- architect.md
|   |-- contract-designer.md
|   |-- frontend-designer.md           + Stitch MCP tools
|   |-- integration-planner.md
|   |-- executor.md                    + 7-skill UI pipeline
|   |-- reviewer.md
|   +-- deployer.md
|-- skills/                            7 skills (each has SKILL.md)
|   |-- start/                         Interactive wizard
|   |-- build/                         Phase-by-phase builder + Stitch pipeline
|   |-- brainstorm/
|   |-- explore/
|   |-- review/
|   |-- deploy/
|   +-- status/
|-- knowledge/                         Ecosystem reference
|   |-- integra-ecosystem.md
|   |-- design-system.md
|   |-- conventions.md
|   |-- product-ideas.md
|   |-- stitch-integration.md          Stitch MCP setup + pipeline
|   |-- ui-skill-pipeline.md           Lean vs full UI pipeline
|   |-- best-practices/                Quality & design guides (NEW)
|   |   |-- ui-ux-design.md            Layout, components, states, Web3 UX
|   |   |-- design-adaptation.md       Color/mood per dApp category + rationale
|   |   |-- performance.md             Next.js, bundle, gas, Core Web Vitals
|   |   |-- accessibility.md           WCAG AA, keyboard, ARIA, contrast
|   |   |-- security-frontend.md       XSS, secrets, tx safety, Stitch safety
|   |   |-- quality-checklist.md       Master A-F grading checklist
|   |   +-- onboarding-ux.md           First-time UX, zero-balance, error mapping
|   |-- design-systems/
|   |   |-- integra-brand.md           Official brand tokens
|   |   +-- custom-brand.md            Custom brand template (ui-ux-pro-max)
|   |-- networks/
|   |   |-- mainnet.md                 Chain ID 26217
|   |   |-- testnet.md                 Chain ID 26218
|   |   |-- shared.md                  IRL/airl, WIRL, precompiles
|   |   +-- tokens.md                  Token registry: IRL, tUSDI, WIRL (NEW)
|   +-- templates/                     Per-category scaffolds (+ token integration)
|       |-- defi-template.md
|       |-- ai-agent-template.md
|       |-- nft-template.md
|       |-- social-template.md
|       |-- gaming-template.md
|       +-- infrastructure-template.md
+-- templates/                         Drop-in config files
    |-- hardhat.config.ts.template
    |-- env.example.template           + STITCH_API_KEY
    |-- web3auth-provider.tsx.template
    +-- tailwind-integra.ts.template
```

---

## Environment setup

The only optional env var for the studio itself:

```bash
# Google Stitch AI UI Generation (optional -- everything works without it)
# Get your key: https://stitch.withgoogle.com/
STITCH_API_KEY=your_key_here
```

Generated projects use the full env template at `templates/env.example.template` which includes network config, Web3Auth, contract addresses, and Stitch.

---

## Principles

**Your idea, not ours.** The studio adapts to your background, your interests, your perspective. It has a conversation, not a menu.

**You decide everything.** Every phase has an approval gate. Nothing happens without your say.

**Works at any level.** Never written Solidity? Fine. Senior blockchain dev? Also fine. The questions adapt.

**Integra-native.** Every generated project uses the [brand system](https://github.com/Integra-layer/integra-brand) (or your custom design), emits XP events, integrates with the ecosystem, and deploys to `*.integralayer.com`.

**Premium UI.** Seven specialized skills ensure every frontend has proper layout, typography, component patterns, animations, and visual polish.

---

<div align="center">

<br />

Built for [Integra](https://integralayer.com)

<br />

```
/integra-studio:start
```

<br />

</div>
