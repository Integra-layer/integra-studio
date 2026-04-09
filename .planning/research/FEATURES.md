# Feature Landscape

**Domain:** Blockchain dApp Developer Studio (Claude Code Plugin)
**Researched:** 2026-04-09
**Overall Confidence:** MEDIUM-HIGH

---

## Table Stakes

Features users expect from a developer studio / dApp scaffolding tool. Missing any of these makes the product feel incomplete compared to Scaffold-ETH 2, ThirdWeb, and Alchemy Create.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| Interactive wizard with selectable options | Every major scaffolding tool (create-eth, create-next-app, ThirdWeb) uses selectable prompts, not free text. Users expect to pick, not type. | Low | V1 uses open-ended questions (anti-pattern for CLI wizards). V2 must switch to selectable options with "Other" escape hatch per WIZ-01/02/03. |
| Contract hot-reload / auto-adapt | Scaffold-ETH 2's signature feature. Frontend auto-updates when contracts change. | Medium | Not directly applicable in Claude Code context, but the equivalent is: re-running build should detect contract changes and regenerate hooks/ABIs automatically. |
| Pre-built contract templates | ThirdWeb's core value prop. ERC-20, ERC-721, marketplace, staking — all one click away. | Low | V1 has category templates in `knowledge/templates/`. These need updating for dual-network and should include deployment-ready Ignition modules. |
| Wallet integration out of the box | Every dApp scaffold ships with wallet connect. Scaffold-ETH uses RainbowKit, ThirdWeb uses their SDK. | Low | V1 already uses Web3Auth via shared provider. Table stakes — just needs dual-network config update. |
| Local development environment | Hardhat local node + faucet + block explorer for testing without touching testnet. | Medium | V1 scaffolds Hardhat config but doesn't set up local dev flow. Should include `hardhat node` scripts and local faucet setup. |
| One-command deploy | `npx hardhat deploy` or equivalent. User shouldn't manually configure deployment. | Low | V1 has `/deploy` skill. Needs dual-network support (mainnet vs testnet target). |
| TypeScript throughout | Every modern scaffold is TypeScript-first. No JS option. | Low | Already in V1. Table stakes. |
| Test scaffolding | Generated tests alongside contracts. Scaffold-ETH includes test templates for every contract. | Low | V1 generates tests in Phase 1. Keep. |
| Network configuration | Pre-configured chain settings, RPC URLs, explorer links. ThirdWeb and Scaffold-ETH both auto-configure. | Low | Critical V2 update: dual-network (mainnet 26217 + testnet 26218) with correct endpoints per NET-01 through NET-09. |
| Environment variable management | `.env.example` with all required vars, `.env` in `.gitignore`. | Low | Already in V1. Table stakes. |
| Design system integration | shadcn/ui + Tailwind is the 2026 standard. ThirdWeb ships styled components. Scaffold-ETH uses daisyUI. | Low | V1 has Integra design system. V2 adds branding toggle (official vs custom). |

## Differentiators

Features that set Integra Studio apart. Not expected by users, but create significant value when present.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| **Google Stitch AI UI generation** | No other blockchain dev studio offers AI-generated, multi-screen UI from natural language. Stitch generates up to 5 interconnected screens, exports HTML/Tailwind, and has an MCP server for agent integration. This is the single biggest differentiator. | High | Stitch SDK (`@google/stitch-sdk`) provides `generate()`, `variants()`, `getHtml()`. MCP via `@_davideast/stitch-mcp`. Pipeline: describe screens in wizard -> Stitch generates variants -> user picks -> export HTML -> integrate into Next.js. Limitation: no persistent design system enforcement across generations — each generation starts somewhat fresh. Mitigation: use `createDesignSystem()` API + DESIGN.md pattern from Antigravity codelab. |
| **6-skill UI/UX pipeline** | No other scaffold produces premium, polished UI. Most generate functional but generic-looking interfaces. A chained skill pipeline (guidelines > pro-max > taste > shadcn > animation > frontend-design) produces output that looks hand-crafted. | Medium | Recommended pipeline order from research: (1) `/web-design-guidelines` for layout/typography/responsiveness foundations, (2) `/ui-ux-pro-max` for creative direction (50+ styles, 161 palettes), (3) `/taste-skill` for spacing/hierarchy/polish, (4) `/shadcn-ui` for consistent component usage, (5) `/ui-animation` for motion, (6) `/frontend-design` for final production code. Alternative simplified pipeline from Anthropic ecosystem: `/frontend-design` -> `/baseline-ui` -> `/fixing-accessibility` -> `/fixing-motion-performance`. |
| **Branding toggle** (official vs custom) | Users building for the Integra ecosystem want official branding. Users building independent dApps want their own identity. No other scaffold offers this as a wizard step. | Medium | Implementation: wizard question "Use Integra official branding or create a custom design?" If official: load `integra-brand` repo tokens (Coral #FF6D49, Euclid Circular B). If custom: invoke `ui-ux-pro-max` to generate a unique design system from user preferences, then save as `DESIGN.md` + CSS variables. Both paths feed into the same Tailwind config structure. |
| **Person-first discovery wizard** | V1's unique strength. Most scaffolds show "pick a template." Integra Studio asks about the person, their background, their pain point, and generates a concept that could only exist because of that conversation. | Low (exists) | Keep this. It's genuinely differentiated. V2 enhancement: after the open-ended discovery phase, switch to selectable options for all technical decisions (network, branding, UI method, scope). Hybrid approach: open for ideation, structured for configuration. |
| **Dual-network awareness** | Most scaffolds target one network. Having mainnet/testnet as a first-class wizard choice with correct gas prices, faucet info, and explorer URLs pre-configured is a quality signal. | Medium | Network selector should be a simple binary choice early in the wizard: "Building for mainnet (production) or testnet (development)?" with clear explanation of each. All generated configs (hardhat, env, Web3Auth, explorer links) auto-adjust. Include decimal precision warning (EVM 18 vs Cosmos 6). |
| **AI agent integration (AgentAuth)** | No other scaffold generates AI agent permission contracts. This is unique to Integra's Agent Arena ecosystem. | Medium | When the dApp concept involves AI agents, auto-include AgentAuth contract with `authorize()`, `revoke()`, `executeAsUser()`. Scaffold agent permission UI components. |
| **XP system as first-class citizen** | Gamification built into every dApp from the start, not bolted on. Every user action emits XP events. | Low (exists) | V1 already does this. Keep. Ensure XP display component and leaderboard are part of standard scaffold. |
| **Extension system** | Scaffold-ETH 2's extension model (`npx create-eth -e [extension]`) is powerful. Integra Studio could offer composable "integration packs" — add GOB trading, add lending, add Agent Arena. | High | Future differentiator. Not V2 scope but worth designing the architecture for. Each integration pack = contract + hooks + UI components + tests. |

## Anti-Features

Features to explicitly NOT build. These seem attractive but create problems.

| Anti-Feature | Why Avoid | What to Do Instead |
|--------------|-----------|-------------------|
| **Template gallery / category picker as first question** | V1's philosophy is correct: person-first, not template-first. Showing "pick DeFi/NFT/Gaming" leads to generic output. Every scaffold tool does this already — it's not a differentiator. | Keep open-ended discovery for ideation. Use selectable options only for technical configuration (network, branding, UI method, scope). |
| **Live Stitch preview server** | PROJECT.md explicitly marks this out of scope. Stitch generates static HTML; building a live preview adds complexity with little value when the HTML can be viewed directly. | Export Stitch HTML, integrate into Next.js, use `npm run dev` for preview. |
| **Light theme** | PROJECT.md: out of scope. Brand guidelines: dark theme default. Adding light theme doubles design surface area for every component. | Dark theme only for Integra branding. If custom branding toggle is selected, the user's generated design system can include light if they want. |
| **Multi-chain deployment** | Integra Studio is for Integra. Supporting arbitrary EVM chains dilutes focus and makes every config conditional. | Single-chain (Integra) with dual-network (mainnet + testnet). Period. |
| **Cosmos SDK native module dev** | PROJECT.md: out of scope. EVM contracts only. Cosmos module development requires Go expertise and different tooling entirely. | EVM smart contracts via Hardhat + Solidity. Reference Cosmos precompiles (staking, bank, etc.) as callable addresses, not as modules to build. |
| **Redux/Zustand state management** | CLAUDE.md says "no Redux/Zustand unless complex." For 90% of dApps, React hooks + wagmi is sufficient. Adding state management libraries increases bundle size and learning curve for beginners. | React hooks + wagmi v2 for contract state. Context for UI state if needed. Only suggest Zustand if the dApp genuinely needs complex client-side state (rare for blockchain apps). |
| **Paid Stitch features** | PROJECT.md: free tier only. Stitch currently has no caps on free tier, but building around paid features creates a dependency risk. | Use only free-tier Stitch APIs. If caps appear, gracefully fall back to manual UI skill pipeline. |

## Feature Dependencies

```
Person-first Discovery (Q1-Q5)
  |
  v
Network Selection (mainnet/testnet) -- no dependencies
  |
  v
Branding Toggle (official/custom) -- no dependencies
  |                                   |
  v                                   v
Official Brand Tokens            Custom Design System (ui-ux-pro-max)
  |                                   |
  +-----------------------------------+
  |
  v
UI Generation Method (Stitch AI / Manual pipeline)
  |                           |
  v                           v
Stitch MCP generates      6-skill pipeline generates
HTML screens               components directly
  |                           |
  +---------------------------+
  |
  v
Contract Scaffolding (parallel with UI) -- requires network selection
  |
  v
Contract Integration (hooks, ABIs) -- requires contracts + frontend
  |
  v
XP Integration -- requires contract integration
  |
  v
Testing + Review -- requires all above
  |
  v
Deploy -- requires network selection + all above
```

## Wizard UX Recommendations

### Selectable Options Pattern (from CLI UX research)

**Use selectable options for all configuration decisions.** Research confirms: the best CLI wizards use staged disclosure (show one question at a time) with selectable defaults and an escape hatch for custom input.

Recommended question format for V2:

```
AskUserQuestion(question: "Which network?\n\n1. Mainnet (production, real IRL tokens)\n2. Testnet (development, free IRL from faucet)\n3. Both (separate configs for each)")
```

**Rules:**
- Max 2-4 options per question (cognitive load research: 3-4 is optimal)
- Always include a "custom" or "other" option as the last choice
- Show the most common/recommended option first
- Include a one-line explanation for each option
- Never ask open-ended questions for technical configuration

**Exception:** The person-first discovery phase (Q1-Q5) stays open-ended. Creativity requires freedom. Configuration requires structure.

### Branding Toggle Pattern

Research shows branding toggles work best as early binary choices, not mid-flow theme pickers:

```
AskUserQuestion(question: "Design approach?\n\n1. Integra official branding (Coral palette, Euclid Circular B font, dark theme)\n2. Custom design (AI generates a unique look for your dApp)")
```

If official: load `integra-brand` tokens directly into Tailwind config and CSS variables.
If custom: run `ui-ux-pro-max` skill to generate palette, typography, and spacing system from the dApp concept. Save as `DESIGN.md` for Stitch and skill pipeline to reference.

### Network Selector UX

Research on multi-chain dApps shows the network should be:
1. **Visible at all times** in the generated dApp UI (badge in header)
2. **Selected once during wizard** with clear consequences explained
3. **Reflected in every config file** (hardhat, env, Web3Auth, explorer links)
4. **Switchable in development** (env var toggle, not rebuild)

Standard pattern from Uniswap/Aave: chain badge in top nav, dropdown to switch, all balances/data update reactively.

For Integra (only 2 networks), simpler: toggle switch in header with "Mainnet" / "Testnet" label and colored indicator (green for mainnet, orange for testnet).

### Google Stitch Integration Pattern

Based on the Stitch SDK and MCP research, the recommended integration flow:

1. **Wizard step:** "Generate UI with Stitch AI or build manually?"
2. **If Stitch:**
   a. Create Stitch project via SDK
   b. If branding toggle = official, call `createDesignSystem()` with Integra tokens
   c. Generate screens from dApp concept (pass PRD context as prompt)
   d. Generate 3 variants per screen
   e. Present variants to user for selection (screenshots via `getImage()`)
   f. Export selected HTML via `getHtml()`
   g. Convert HTML to Next.js components (agent task)
   h. Run UI skill pipeline for polish
3. **If manual:**
   a. Run full 6-skill UI pipeline
   b. Generate components directly in Next.js

**Key limitation:** Stitch has no persistent design system enforcement. Each generation can drift. Mitigation: generate a `DESIGN.md` from the chosen design system (official or custom), pass it as context to every Stitch prompt, and use `createDesignSystem()` API per-project.

### UI/UX Skill Pipeline

Two pipeline options based on research:

**Full pipeline (maximum quality, slower):**
1. `/web-design-guidelines` -- layout foundations, typography scale, responsive breakpoints
2. `/ui-ux-pro-max` -- creative direction, color palette, style identity
3. `/taste-skill` -- spacing harmony, visual hierarchy, micro-details
4. `/shadcn-ui` -- component consistency, proper shadcn/ui usage
5. `/ui-animation` -- motion design, transitions, reduced-motion support
6. `/frontend-design` -- final production code with all above applied

**Lean pipeline (good enough for most dApps, faster):**
1. `/frontend-design` -- initial design direction + production code
2. `/baseline-ui` -- polish spacing, typography, states
3. `/fixing-accessibility` -- keyboard nav, screen readers, semantic HTML
4. `/fixing-motion-performance` -- optimized animations

**Recommendation:** Default to lean pipeline. Offer full pipeline as an option for users who selected "custom design" branding. Official Integra branding already has design decisions made, so the lean pipeline suffices.

## MVP Recommendation

### Must-have for V2 launch:

1. **Selectable wizard options** (WIZ-01/02/03) -- highest impact UX improvement, low complexity
2. **Dual-network support** (NET-01 through NET-09) -- critical correctness fix, medium complexity
3. **Branding toggle** (BRAND-01/02/03) -- key differentiator, medium complexity
4. **Network selector in wizard** (WIZ-04) -- required for dual-network, low complexity
5. **Updated chain config** (CONFIG-01 through CONFIG-06) -- correctness, must ship

### Should-have for V2 launch:

6. **Google Stitch integration** (STITCH-01 through STITCH-05) -- biggest differentiator but high complexity
7. **UI skill pipeline** (UISKILL-01 through UISKILL-07) -- quality differentiator, medium complexity

### Defer:

- Extension system -- design the architecture now, build later
- Live Stitch preview -- out of scope per PROJECT.md
- Light theme -- out of scope per brand guidelines

## Competitive Landscape Summary

| Feature | Scaffold-ETH 2 | ThirdWeb | Integra Studio V2 |
|---------|----------------|----------|-------------------|
| Scaffolding method | CLI (`npx create-eth`) | Dashboard + CLI | Claude Code wizard |
| Template system | Extensions (modular) | Template marketplace | Person-first discovery + templates |
| Contract hot-reload | Yes (signature feature) | No | Partial (rebuild detects changes) |
| Wallet integration | RainbowKit | ThirdWeb SDK | Web3Auth (social login) |
| AI UI generation | No | No | **Yes (Google Stitch)** |
| UI skill pipeline | No | No | **Yes (6 skills)** |
| Design system | daisyUI | ThirdWeb styled | **Branding toggle (official/custom)** |
| Network support | Multi-chain | Multi-chain | Dual-network (mainnet/testnet) |
| AI agent integration | No | No | **Yes (AgentAuth)** |
| Gamification (XP) | No | No | **Yes (built-in)** |
| Target audience | Experienced devs | All levels | **Zero blockchain experience** |
| AI-ready config | Yes (.claude/, .cursor/) | No | **Native Claude Code plugin** |

## Sources

- [Scaffold-ETH 2](https://scaffoldeth.io/) -- features, extensions, architecture (HIGH confidence)
- [Scaffold-ETH 2 Extensions](https://scaffoldeth.io/extensions) -- extension model (HIGH confidence)
- [ThirdWeb Templates](https://thirdweb.com/templates) -- template marketplace (MEDIUM confidence)
- [Google Stitch Official](https://stitch.withgoogle.com/) -- product page (HIGH confidence)
- [Google Stitch Blog](https://blog.google/innovation-and-ai/models-and-research/google-labs/stitch-ai-ui-design/) -- feature overview (HIGH confidence)
- [Google Developers Blog - Stitch](https://developers.googleblog.com/stitch-a-new-way-to-design-uis/) -- launch announcement (HIGH confidence)
- [Stitch SDK on GitHub](https://github.com/google-labs-code/stitch-sdk) -- API methods, code export (HIGH confidence)
- [Stitch MCP by David East](https://github.com/davideast/stitch-mcp) -- MCP integration (HIGH confidence)
- [Design-to-Code Codelab](https://codelabs.developers.google.com/design-to-code-with-antigravity-stitch) -- Stitch + agent pipeline (HIGH confidence)
- [Claude Code UI Skills (DEV.to)](https://dev.to/blamsa0mine/claude-code-skills-install-ui-skills-build-a-frontend-design-workflow-claude-code-cursorvs-4n43) -- skill pipeline order (MEDIUM confidence)
- [Top 8 CLI UX Patterns (Medium)](https://medium.com/@kaushalsinh73/top-8-cli-ux-patterns-users-will-brag-about-4427adb548b7) -- wizard patterns (MEDIUM confidence)
- [NNGroup Progressive Disclosure](https://www.nngroup.com/articles/progressive-disclosure/) -- UX research (HIGH confidence)
- [Google Stitch March 2026 Update](https://tech-insider.org/google-stitch-ai-design-tool-march-2026-update/) -- multi-screen, canvas, design systems (LOW confidence -- single source)
- [Anthropic Frontend Design Skill](https://claude.com/blog/improving-frontend-design-through-skills) -- official skill docs (HIGH confidence)
