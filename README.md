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
[![Chain](https://img.shields.io/badge/Chain-integra--testnet--1-00A186?style=flat-square)]()

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

## What happens

```
 You                          Studio
  │                             │
  │  "Tell me about yourself"   │
  │◄────────────────────────────│
  │                             │
  │  (you talk about your       │
  │   background, interests,    │
  │   problems you've seen)     │
  │────────────────────────────►│
  │                             │
  │  Connects your world to     │
  │  what's possible on Integra │
  │◄────────────────────────────│
  │                             │
  │  You shape the idea         │
  │  together through           │
  │  conversation               │
  │◄───────────────────────────►│
  │                             │
  │         ┌───────────┐       │
  │         │ 4 agents  │       │
  │         │ design it │       │
  │         │ in parallel│      │
  │         └─────┬─────┘       │
  │               │             │
  │  "Does this look right?"    │
  │◄────────────────────────────│
  │                             │
  │  Project scaffolded         │
  │  with docs, contracts,      │
  │  frontend, integrations     │
  │◄────────────────────────────│
  │                             │
  │  /integra-studio:build      │
  │  (phase by phase,           │
  │   you approve each one)     │
  │◄───────────────────────────►│
  │                             │
  │  yourapp.integralayer.com   │
  │◄────────────────────────────│
```

The wizard doesn't pick for you. It listens, asks follow-ups based on who you are, and helps you find an idea that only you would come up with. Two people never get the same result.

---

## Commands

| Command | When to use it |
|---------|---------------|
| `/integra-studio:start` | You want to build something (or figure out what to build) |
| `/integra-studio:brainstorm` | You just want to explore ideas, no commitment |
| `/integra-studio:build` | Your project is scaffolded, time to write code |
| `/integra-studio:explore` | You want to learn about an Integra feature |
| `/integra-studio:review` | Check your code for security and quality |
| `/integra-studio:deploy` | Ship to testnet |
| `/integra-studio:status` | See where your project stands |

---

## How it's built

### Agents

Eight agents handle different parts of the work:

```
discovery          Understands who you are and what you want
architect          Designs the system — files, stack, data flow
contract-designer  Writes Solidity interfaces and specs
frontend-designer  Designs pages, components, user flows
integration-planner Maps connections to the Integra ecosystem
executor           Writes the actual code
reviewer           Checks security, quality, brand compliance
deployer           Ships to testnet and configures subdomain
```

They run in parallel when possible.

### Knowledge

The studio knows the Integra ecosystem:

```
Chain         integra-testnet-1 · EVM 26218 · IRL token
Products      Asset Passport · Wrapper · GOB · Agent Arena
Infra         Web3Auth · AgentAuth · XP System · Faucet
Standards     ERC-7943 · ERC-3643 · ERC-8004 · ERC-6551
Advanced      Gasless ops · IBC · Token Bridge · Agent Passport
```

Plus the full [Integra brand system](https://github.com/Integra-layer/integra-brand) — colors, fonts, gradients, components.

### Templates

When it scaffolds a project, it follows Integra conventions:

- **Contracts** — Solidity 0.8.24+, Hardhat, OpenZeppelin
- **Frontend** — Next.js 14+, TypeScript, Tailwind, shadcn/ui
- **Wallet** — Web3Auth (Google, X, Email login)
- **Design** — [integra-brand](https://github.com/Integra-layer/integra-brand) tokens and kit
- **Deployment** — `yourapp.integralayer.com` via Caddy

---

## Project structure

```
integra-studio/
├── CLAUDE.md                          Main instructions
├── agents/                            8 agent definitions
│   ├── discovery.md
│   ├── architect.md
│   ├── contract-designer.md
│   ├── frontend-designer.md
│   ├── integration-planner.md
│   ├── executor.md
│   ├── reviewer.md
│   └── deployer.md
├── skills/                            7 skills (each has SKILL.md)
│   ├── start/
│   ├── build/
│   ├── brainstorm/
│   ├── explore/
│   ├── review/
│   ├── deploy/
│   └── status/
├── knowledge/                         Ecosystem reference
│   ├── integra-ecosystem.md
│   ├── design-system.md
│   ├── conventions.md
│   ├── product-ideas.md
│   └── templates/                     Per-category scaffolds
│       ├── defi-template.md
│       ├── ai-agent-template.md
│       ├── nft-template.md
│       ├── social-template.md
│       ├── gaming-template.md
│       └── infrastructure-template.md
└── templates/                         Drop-in config files
    ├── hardhat.config.ts.template
    ├── env.example.template
    ├── web3auth-provider.tsx.template
    └── tailwind-integra.ts.template
```

---

## Principles

**Your idea, not ours.** The studio adapts to your background, your interests, your perspective. It never shows a menu of categories. It has a conversation.

**You decide everything.** Every phase has an approval gate. Nothing happens without your say.

**Works at any level.** Never written Solidity? Fine. Senior blockchain dev? Also fine. The questions adapt.

**Integra-native.** Every generated project uses the official [brand system](https://github.com/Integra-layer/integra-brand), emits XP events, integrates with the ecosystem, and deploys to `*.integralayer.com`.

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
