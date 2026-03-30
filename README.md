<div align="center">

<img src="https://img.shields.io/badge/Integra-Developer%20Studio-6C5CE7?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0id2hpdGUiPjxwYXRoIGQ9Ik0xMiAyTDIgNy41VjE2LjVMMTIgMjJMMjIgMTYuNVY3LjVMMTIgMlpNMTIgNC4yN0wxOS43MyA4LjVMMTIgMTIuNzNMNC4yNyA4LjVMMTIgNC4yN1oiLz48L3N2Zz4=&logoColor=white" alt="Integra Studio" />

# Integra Developer Studio

### From zero to deployed dApp on the Integra blockchain.

An interactive wizard + multi-agent system that guides you through designing, building, and deploying dApps — even if you've never touched blockchain before.

<br />

[![Claude Code Plugin](https://img.shields.io/badge/Claude%20Code-Plugin-A29BFE?style=flat-square)](https://claude.ai/claude-code)
[![License](https://img.shields.io/badge/License-MIT-00CEC9?style=flat-square)]()
[![Agents](https://img.shields.io/badge/Agents-8-F0B90B?style=flat-square)]()
[![Skills](https://img.shields.io/badge/Skills-7-FF6B6B?style=flat-square)]()
[![dApp Ideas](https://img.shields.io/badge/Ideas-25+-00B894?style=flat-square)]()

</div>

---

## The Problem

You want to build a dApp on Integra. But:

- You don't know what to build
- You don't know Solidity, or the Integra ecosystem, or how Web3Auth works
- You don't want to spend 3 days reading docs before writing a single line of code
- You need something that works on testnet by April 15

## The Solution

Run one command. Answer a few questions. Get a fully architected, scaffolded project with contracts, frontend, and integrations — ready to build.

```
/integra-studio:start
```

The wizard asks, you decide. It never assumes.

---

## Quick Start

```bash
# Load the plugin
claude --plugin-dir /path/to/integra-studio

# Start the wizard
/integra-studio:start

# Or brainstorm first if you have no idea
/integra-studio:brainstorm
```

---

## How It Works

```
                    YOU
                     |
                     v
        /integra-studio:start
                     |
     ┌───────────────┼───────────────┐
     |               |               |
  "What kind    "Describe it     "Which Integra
   of dApp?"    in one sentence"  features?"
     |               |               |
     v               v               v
  ┌──────────────────────────────────────┐
  |         DISCOVERY (8 questions)       |
  |  MCQ selectors + open questions       |
  |  "I don't know" → we suggest ideas    |
  └──────────────────┬───────────────────┘
                     |
                     v
  ┌──────────────────────────────────────┐
  |         SYNTHESIS (4 agents)          |
  |                                       |
  |  architect ──────► system design      |
  |  contract-designer ► Solidity specs   |
  |  frontend-designer ► UI/UX specs      |
  |  integration-planner ► ecosystem hooks|
  |                                       |
  |  All run in parallel                  |
  └──────────────────┬───────────────────┘
                     |
              "Does this look right?"
                     |
                     v
  ┌──────────────────────────────────────┐
  |         PROJECT SETUP                 |
  |                                       |
  |  docs/PRD.md                          |
  |  docs/ARCHITECTURE.md                 |
  |  docs/CONTRACTS.md                    |
  |  docs/FRONTEND.md                     |
  |  docs/INTEGRATIONS.md                 |
  |  hardhat.config.ts                    |
  |  next.config.ts                       |
  |  package.json                         |
  └──────────────────┬───────────────────┘
                     |
                     v
        /integra-studio:build
                     |
     Phase 1: Contracts (compile + test)
     Phase 2: Frontend (pages + layout)
     Phase 3: Integration (hooks + ABIs)
     Phase 4: Polish (design system)
     Phase 5: XP + ecosystem
     Phase 6: Testing + review
                     |
          ┌──── approval gate ────┐
          |  at every phase       |
          |  you decide to        |
          |  proceed, edit,       |
          |  or iterate           |
          └───────────────────────┘
                     |
                     v
        /integra-studio:deploy
                     |
     Contracts → Explorer → Frontend → Subdomain
                     |
                     v
          yourapp.integralayer.com ✓
```

---

## Skills

| Command | What it does |
|---------|-------------|
| `/integra-studio:start` | Interactive wizard — design your dApp from scratch |
| `/integra-studio:build` | Build phase by phase with approval at each step |
| `/integra-studio:brainstorm` | Get 5 dApp ideas tailored to Integra. Pick one or ask for more |
| `/integra-studio:explore` | Interactive reference for any Integra feature |
| `/integra-studio:review` | Security + quality + compliance check on your code |
| `/integra-studio:deploy` | Ship to testnet — contracts, frontend, subdomain |
| `/integra-studio:status` | See what's built, what's remaining, next steps |

---

## Agents

Eight specialized agents handle different aspects of your project:

| Agent | Model | Role |
|-------|-------|------|
| **discovery** | Sonnet | Runs the questionnaire, understands your idea |
| **architect** | Opus | Designs system architecture, file structure, tech stack |
| **contract-designer** | Sonnet | Writes Solidity interfaces, events, security patterns |
| **frontend-designer** | Sonnet | Designs UI/UX, component trees, user flows |
| **integration-planner** | Haiku | Maps Integra ecosystem hooks — which contracts to call, which events to emit |
| **executor** | Sonnet | Writes the actual code — contracts, components, hooks, configs |
| **reviewer** | Opus | Reviews for security, quality, design system compliance |
| **deployer** | Sonnet | Deploys to testnet, verifies contracts, configures subdomain |

Agents run in parallel when possible (architect + contract-designer + frontend-designer + integration-planner all at once during synthesis).

---

## What You Can Build

The studio includes **25+ curated dApp ideas** across 6 categories:

### DeFi
Fractional RE pools, yield vaults, prediction markets, collateral swaps, staking dashboards

### AI Agents
Trading agents, portfolio rebalancers, tenant advocates, negotiator bots, SDK copilots

### Social
Builder profiles, agent reputation DAOs, ecosystem activity feeds

### Gaming
Agent battle arenas, property tycoon games, prediction tournaments

### NFT / Collectibles
Agent achievement badges, passport galleries, dynamic portfolio NFTs

### Infrastructure
Analytics dashboards, price oracles, subdomain managers, contract wizards

Don't see your idea? The wizard handles **custom** projects too — just describe what you want.

---

## Integra Ecosystem Knowledge

The studio has deep knowledge of the entire Integra stack baked in:

```
Chain Config          integra-testnet-1 / EVM 26218 / IRL token
─────────────────────────────────────────────────────────
Core Products         Asset Passport (ERC-721)
                      Wrapper / IRWA Token (ERC-20/721/3643)
                      Global Order Book (trade + negotiate)
                      Agent Arena (AI marketplace)
─────────────────────────────────────────────────────────
Infrastructure        Web3Auth (social login)
                      AgentAuth.sol (agent permissions)
                      XP System (cross-app gamification)
                      Faucet (testnet IRL)
                      Domain Router (*.integralayer.com)
─────────────────────────────────────────────────────────
Standards             ERC-7943 (Universal RWA Interface)
                      ERC-3643 (T-REX Security Token)
                      ERC-8004 (Agent Identity Registry)
                      ERC-6551 (Token Bound Accounts)
─────────────────────────────────────────────────────────
Advanced              Gasless ops (x/feegrant + x/authz)
                      IBC cross-chain agent operations
                      IRWA Token Bridge (lock-and-mint)
                      Agent Passport Protocol (stake + slash)
```

Plus references to:
- 84 documents in the Integra knowledge bank
- 13 deep research campaigns (300+ sources)
- Security audit findings and remediation guides
- Regulatory frameworks (UAE, Switzerland, Japan, Singapore)
- Neon Noir brand design system (357KB of research)

---

## Tech Stack (What Gets Generated)

Every dApp follows Integra conventions:

| Layer | Tech |
|-------|------|
| **Contracts** | Solidity 0.8.24+, Hardhat, OpenZeppelin, Ignition deploys |
| **Frontend** | Next.js 14+ (App Router), TypeScript strict, Tailwind CSS |
| **Components** | shadcn/ui customized to Integra dark theme |
| **Wallet** | Web3Auth Modal (Google, X, Email login) |
| **Contract Interaction** | wagmi v2 + viem |
| **Design** | Dark theme only — `#0A0A1A` bg, `#6C5CE7` brand, `#F0B90B` gold |
| **Testing** | Hardhat test (contracts), Vitest (frontend) |
| **Deployment** | Caddy subdomain at `yourapp.integralayer.com` |

---

## Design System

All generated frontends follow the Integra design system automatically:

```
Background     #0A0A1A          Cards          #12122A
Brand Purple   #6C5CE7          Brand Light    #A29BFE
Gold           #F0B90B          Teal           #00CEC9
Success        #00B894          Danger         #FF6B6B
Text           #E8E8F0          Text Dim       #8888AA
Border         #2A2A4A          Text Bright    #FFFFFF
```

Dark theme only. Inter font. 16px card radius. Subtle borders. No flashy animations.

---

## Project Structure

```
integra-studio/
├── .claude/
│   └── CLAUDE.md                    # Brain — ecosystem knowledge, conventions, routing
├── agents/
│   ├── discovery.md                 # Interactive questionnaire
│   ├── architect.md                 # System design (Opus)
│   ├── contract-designer.md         # Solidity specs
│   ├── frontend-designer.md         # UI/UX design
│   ├── integration-planner.md       # Ecosystem hooks
│   ├── executor.md                  # Code writer
│   ├── reviewer.md                  # Quality + security (Opus)
│   └── deployer.md                  # Testnet deployment
├── skills/
│   ├── start.md                     # Main wizard (16KB)
│   ├── build.md                     # Phase-by-phase execution
│   ├── brainstorm.md                # Idea generation
│   ├── explore.md                   # Ecosystem explorer
│   ├── review.md                    # Code review
│   ├── deploy.md                    # Deployment pipeline
│   └── status.md                    # Progress tracker
├── knowledge/
│   ├── integra-ecosystem.md         # Full ecosystem reference
│   ├── design-system.md             # Colors, typography, components
│   ├── conventions.md               # Code conventions + security
│   ├── product-ideas.md             # 25+ curated dApp ideas
│   └── templates/                   # Category-specific templates
│       ├── defi-template.md
│       ├── ai-agent-template.md
│       ├── nft-template.md
│       ├── social-template.md
│       ├── gaming-template.md
│       └── infrastructure-template.md
├── templates/                       # Scaffold files for new projects
│   ├── hardhat.config.ts.template
│   ├── env.example.template
│   ├── web3auth-provider.tsx.template
│   └── tailwind-integra.ts.template
└── manifest.json
```

---

## Philosophy

**Ask, never assume.** The user decides everything. The studio suggests, explains trade-offs, and executes — but every decision goes through you.

**Works for everyone.** Whether you're a senior Solidity dev or someone who's never seen a smart contract, the wizard adapts. Vague answers get concrete suggestions. "I don't know" triggers brainstorm mode.

**Incremental, not monolithic.** Six build phases with approval gates. You can stop, iterate, or change direction at any point. Nothing is irreversible until deployment.

**Convention over configuration.** Every generated project follows Integra standards: design system, code patterns, security practices, XP integration. No decisions about tooling — just your dApp logic.

---

<div align="center">

Built for the [Integra](https://integralayer.com) ecosystem.

**8 agents** | **7 skills** | **25+ ideas** | **1 command to start**

```
/integra-studio:start
```

</div>
