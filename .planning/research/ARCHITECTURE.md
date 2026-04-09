# Architecture Patterns

**Domain:** Claude Code plugin for multi-network blockchain dApp scaffolding
**Researched:** 2026-04-09

---

## 1. Network Config: Knowledge Files, Not Inline CLAUDE.md

**Recommendation:** Move all network configuration into separate knowledge files. Do NOT inline in CLAUDE.md.

**Confidence:** HIGH (based on Claude Code docs + current plugin structure analysis)

### Current Problem

CLAUDE.md currently inlines testnet-only config (chain ID 26218, single RPC). The ecosystem knowledge file (`knowledge/integra-ecosystem.md`) also has config, but it contradicts CLAUDE.md (chain ID 26217 vs 26218). Templates (`hardhat.config.ts.template`, `web3auth-provider.tsx.template`) hardcode testnet values. This triple-source-of-truth is the root cause of configuration drift.

### Recommended Architecture

```
knowledge/
  networks/
    mainnet.md          # All mainnet config: chain IDs, RPCs, explorers, gas, precompiles
    testnet.md          # All testnet config: chain IDs, RPCs, explorers, faucet
    shared.md           # Token info (IRL/airl), decimal warnings, WIRL address
  integra-ecosystem.md  # Products, contracts, APIs (no network config)
  design-system.md      # Brand tokens (unchanged)
  conventions.md        # Coding conventions (unchanged)
```

### Why Separate Files

1. **Single source of truth per network.** When mainnet RPC changes, update one file. Agents and templates reference it.
2. **Claude Code auto-loads knowledge/ files.** They appear in context when relevant, so agents always have correct config.
3. **CLAUDE.md stays stable.** It describes behavior and rules, not data that changes. Network data is volatile; rules are not.
4. **Template rendering.** Templates can reference `{{network.rpcUrl}}` style placeholders resolved by the executor agent reading the appropriate knowledge file based on the wizard's network selection.

### Network File Structure (example: `knowledge/networks/mainnet.md`)

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
| Finality | Instant (CometBFT) |

## Precompiled Contracts
| Contract | Address |
|----------|---------|
| WIRL | 0x5002000000000000000000000000000000000001 |
| Staking | ... |
| Distribution | ... |
```

### What Stays in CLAUDE.md

CLAUDE.md retains:
- Behavioral rules (always use AskUserQuestion, never assume)
- Design system reference (pointer to `knowledge/design-system.md`)
- Agent routing table
- Security checklist
- Skill triggers
- A one-liner: "Network config is in `knowledge/networks/`. Never hardcode chain IDs or RPCs."

### Template Updates

Templates become network-aware with comments, not hardcoded values:

```typescript
// hardhat.config.ts.template
networks: {
  "integra-mainnet": {
    url: process.env.INTEGRA_MAINNET_RPC_URL || "{{mainnet.evmRpc}}",
    chainId: {{mainnet.chainId}},
    accounts: process.env.DEPLOYER_PRIVATE_KEY ? [process.env.DEPLOYER_PRIVATE_KEY] : [],
  },
  "integra-testnet": {
    url: process.env.INTEGRA_TESTNET_RPC_URL || "{{testnet.evmRpc}}",
    chainId: {{testnet.chainId}},
    accounts: process.env.DEPLOYER_PRIVATE_KEY ? [process.env.DEPLOYER_PRIVATE_KEY] : [],
  },
},
```

The executor agent reads the network knowledge file and fills placeholders. This is simpler than a build-time template engine -- the agent IS the template engine.

---

## 2. Google Stitch MCP: Integrate Into Frontend-Designer Agent, Not a Separate Agent

**Recommendation:** Wire Stitch MCP as a tool available to the existing `frontend-designer` agent. Do NOT create a separate `stitch` agent.

**Confidence:** MEDIUM (Stitch MCP is relatively new; the tool surface is small enough to embed)

### Rationale

Stitch MCP exposes exactly 3 tools:
- `build_site` -- builds a site from a Stitch project, mapping screens to routes
- `get_screen_code` -- retrieves HTML for a single screen
- `get_screen_image` -- retrieves a screenshot as base64

These are UI design tools. The frontend-designer agent already owns UI design decisions (page layouts, component trees, user flows). Adding Stitch as a tool to this agent is a natural extension, not a new responsibility.

A separate stitch agent would create coordination overhead: the frontend-designer would need to hand off to stitch, wait for results, then re-integrate. This adds a round-trip with no benefit since the decision of WHAT to generate in Stitch requires the same context the frontend-designer already holds (the discovery brief, architecture, component tree).

### Integration Pattern

```
frontend-designer agent
  tools: [Read, Grep, Glob, mcp__stitch__build_site, mcp__stitch__get_screen_code, mcp__stitch__get_screen_image]
```

The agent's workflow becomes:

1. Design component tree and page map (existing behavior)
2. IF user chose "Generate UI with Stitch AI" in wizard:
   a. Call `build_site` with screen-to-route mapping
   b. Call `get_screen_code` for each screen
   c. Include Stitch HTML in the Frontend Design Document as "reference implementations"
   d. Note which screens have Stitch-generated HTML vs. need manual build
3. IF user chose "Build manually":
   a. Skip Stitch calls entirely
   b. Produce component tree as today

### MCP Configuration

Add to project `.mcp.json`:

```json
{
  "mcpServers": {
    "stitch": {
      "command": "npx",
      "args": ["@_davideast/stitch-mcp", "proxy"],
      "env": {
        "STITCH_API_KEY": "${STITCH_API_KEY}"
      }
    }
  }
}
```

### Stitch Output Integration

Stitch outputs raw HTML/CSS. The executor agent converts this to Next.js components:
- HTML structure becomes JSX in page components
- CSS becomes Tailwind classes (manual or via conversion)
- Stitch design tokens get replaced with Integra design system tokens

The frontend-designer document should flag: "This page has Stitch reference HTML. Executor: use as layout guide, replace styles with Integra design system, convert to React components."

### When Stitch Is Not Available

If the user has no Google account or no `STITCH_API_KEY`, the frontend-designer falls back to its current behavior (pure component tree + wireframe descriptions). This makes Stitch purely additive -- it never blocks the pipeline.

---

## 3. UI/UX Skills Pipeline: Sequential with Early Exit, Not Parallel

**Recommendation:** Chain the 6 UI/UX skills sequentially in a fixed order during the build phase's UI Polish step (Phase 4). Each skill reads the previous skill's output. NOT parallel.

**Confidence:** HIGH (skills produce design decisions that downstream skills depend on)

### Why Sequential

The 6 skills have clear dependencies:

```
web-design-guidelines  (layout, typography, responsiveness rules)
       |
       v
ui-ux-pro-max          (applies 50+ styles, 161 palettes to the rules)
       |
       v
taste-skill            (refines spacing, hierarchy, visual weight)
       |
       v
shadcn-UI              (maps refined design to shadcn component variants)
       |
       v
UI-animation           (adds motion to finalized components)
       |
       v
frontend-design        (produces final component tree with all polish applied)
```

Running these in parallel would produce conflicting decisions. For example, `taste-skill` might adjust spacing that `shadcn-UI` then overrides. The sequential pipeline ensures each skill builds on the previous one's output.

### Implementation: Orchestrator Skill

Create a new orchestrator skill that chains the 6 skills:

```
skills/
  ui-pipeline/
    SKILL.md      # Orchestrator that invokes skills sequentially
```

**SKILL.md frontmatter:**
```yaml
---
name: ui-pipeline
description: Orchestrates the 6-skill UI/UX pipeline for premium design output
user_invocable: false
---
```

The orchestrator:
1. Reads the current Frontend Design Document
2. For each skill in order, spawns a subagent with the skill's instructions + accumulated context
3. Each subagent writes its refinements to a shared `.ui-pipeline/` staging directory
4. After all 6 complete, the orchestrator merges into the final Frontend Design Document

### Build Phase Integration

The build skill's Phase 4 (UI Polish) currently says "Apply Integra design system colors, typography, spacing." This becomes:

```
Phase 4: UI Polish
  Step 1: Run ui-pipeline skill (sequential chain of 6 skills)
  Step 2: Executor applies the pipeline's output
  Step 3: Visual review
```

### Early Exit

If the user chose "Use Integra official branding" in the wizard, `ui-ux-pro-max` (which generates custom palettes) runs in "Integra mode" -- it enforces the brand palette rather than generating a new one. The skill chain still runs (spacing, animation, etc. still benefit from refinement) but palette generation is skipped.

If the user chose "Build manually" (no Stitch), all 6 skills still run. Stitch and the UI pipeline are independent -- Stitch provides reference HTML, the pipeline provides design system refinement.

---

## 4. Selectable-Only Wizard: AskUserQuestion with Numbered Options + "Other" Escape Hatch

**Recommendation:** Every wizard question uses AskUserQuestion with 2-4 numbered options. Always include a free-text escape hatch as the last option.

**Confidence:** HIGH (AskUserQuestion supports this pattern natively; current wizard already partially does this)

### Current Problem

The `/start` wizard currently uses open-ended questions ("Tell me about yourself", "What bugs you?"). This is intentional for V1 (person-first, no templates). V2 requirements (WIZ-01 through WIZ-06) mandate selectable options for ALL questions.

### Tension: Person-First vs. Selectable-Only

The V1 philosophy of "never show pre-made lists" directly conflicts with V2's "every question has concrete selectable choices." Resolution: keep the person-first spirit by making options CONTEXTUAL, not generic. The wizard adapts its options based on previous answers.

### Pattern

```
AskUserQuestion:
  question: |
    What kind of experience are you building?

    1. Trading & Finance (DEX, lending, staking, yield)
    2. Real-World Assets (tokenize property, gold, bonds)
    3. AI & Agents (autonomous bots, agent marketplace)
    4. Let me describe something else...
  options: ["Trading & Finance", "Real-World Assets", "AI & Agents", "Let me describe..."]
```

Key rules:
- **2-4 concrete options** per question (never more than 4 -- decision fatigue)
- **Last option is always free-text** ("Let me describe...", "Something else...", "I have a different idea...")
- **Options adapt** based on prior answers. If user selected "Real-World Assets", the next question's options focus on RWA subtypes, not generic dApp features.
- **Never show the same options twice** across the wizard flow

### AskUserQuestion Constraints

From research:
- AskUserQuestion has a **60-second timeout** -- keep questions short
- **Cannot be called from subagents** -- only the main orchestrator (skill) can ask
- Supports `multiSelect: true` for questions where multiple answers are valid (e.g., "Which Integra features?")

This means: the `/start` wizard skill must own ALL user interactions. It cannot delegate questions to the discovery agent running as a subagent. The discovery agent's role changes from "interactive questionnaire" to "context analyzer" -- it receives the wizard's collected answers and produces the Discovery Brief.

### Revised Wizard Flow

```
/start skill (owns all AskUserQuestion calls)
  Q1: Network selection (mainnet / testnet)
  Q2: Branding (Integra official / custom design)
  Q3: UI generation (Stitch AI / manual build)
  Q4: dApp category (4 options + other)
  Q5: Specific idea within category (adaptive options + other)
  Q6: Scope (prototype / MVP / full)
  Q7: Name (3 generated suggestions + "I have a name")
       |
       v
  Pass all answers to discovery agent (subagent, no user interaction)
       |
       v
  Discovery agent produces Discovery Brief
       |
       v
  Show brief, AskUserQuestion: "Looks good? (yes / tweak / start over)"
```

---

## 5. Branding Toggle: Separate Design System Files Per Brand

**Recommendation:** Maintain two design system files. The wizard's branding choice determines which one gets loaded into context.

**Confidence:** HIGH (cleanest separation; avoids conditional logic pollution in a single file)

### Architecture

```
knowledge/
  design-systems/
    integra-brand.md     # Official Integra branding (Coral #FF6D49, Euclid Circular B, etc.)
    custom-brand.md      # Template for ui-ux-pro-max generated branding
  design-system.md       # REMOVED -- replaced by the two files above
```

### How It Works

1. **Wizard Q2** asks: "Use Integra official branding or custom design?"
2. The `/start` skill writes a `.integra/config.json` in the scaffolded project:
   ```json
   {
     "network": "mainnet",
     "branding": "integra",
     "uiGeneration": "stitch"
   }
   ```
3. The `build` skill reads `.integra/config.json` at the start of each phase.
4. Based on `branding`:
   - `"integra"` -- loads `knowledge/design-systems/integra-brand.md` into agent context. The executor uses the official Integra design system verbatim. The UI pipeline's `ui-ux-pro-max` step runs in "enforce brand" mode.
   - `"custom"` -- the UI pipeline's `ui-ux-pro-max` generates a custom palette and writes it to `knowledge/design-systems/custom-brand.md`. Subsequent pipeline skills and the executor use this generated file.

### Why Not Conditional Sections in One File

Conditional sections (e.g., `{% if branding == 'integra' %}`) are not supported in Claude Code markdown files. You would need to rely on the agent reading a flag and ignoring half the file, which is fragile. Two files means:
- The agent's context only contains the RELEVANT design system
- No risk of the agent mixing brand tokens
- Each file is self-contained and can be updated independently

### integra-brand.md Content

This is essentially the current `design-system.md` with the corrections from PROJECT.md:
- Primary color: Coral `#FF6D49` (not purple `#6C5CE7`)
- Font: Euclid Circular B (not Inter)
- Forbidden colors list enforced
- Dark theme surfaces from the integra-brand repo

### custom-brand.md Content (Template)

```markdown
# Custom Design System

> Generated by ui-ux-pro-max for [project-name]

## Colors
(filled by ui-ux-pro-max during UI pipeline)

## Typography
(filled by ui-ux-pro-max -- can use any font)

## Surfaces
(filled by ui-ux-pro-max)

## Components
(filled by shadcn-UI skill during UI pipeline)
```

---

## 6. Component Boundaries and Data Flow

### Overall Plugin Architecture

```
integra-studio/
  CLAUDE.md                    # Behavioral rules, agent routing, security checklist
  .mcp.json                    # Stitch MCP server config
  agents/
    discovery.md               # Context analyzer (no longer interactive)
    architect.md               # System design
    contract-designer.md       # Solidity specs
    frontend-designer.md       # UI design + Stitch integration
    integration-planner.md     # Ecosystem hooks
    executor.md                # Code builder
    reviewer.md                # Quality/security check
    deployer.md                # Deployment
  skills/
    start/SKILL.md             # Wizard (owns all AskUserQuestion calls)
    build/SKILL.md             # Phase-by-phase build orchestrator
    ui-pipeline/SKILL.md       # 6-skill UI/UX chain (NEW)
    review/SKILL.md            # Quality review
    deploy/SKILL.md            # Deployment
    research/SKILL.md          # Domain research
    brainstorm/SKILL.md        # Idea generation
    explore/SKILL.md           # Ecosystem explorer
    status/SKILL.md            # Progress check
  knowledge/
    networks/
      mainnet.md               # Mainnet config (NEW)
      testnet.md               # Testnet config (NEW)
      shared.md                # Cross-network info (NEW)
    design-systems/
      integra-brand.md         # Official branding (MOVED + UPDATED)
      custom-brand.md          # Template for generated branding (NEW)
    integra-ecosystem.md       # Products, APIs, contracts (UPDATED)
    conventions.md             # Coding conventions
    product-ideas.md           # Idea catalog
  templates/
    hardhat.config.ts.template # Dual-network (UPDATED)
    env.example.template       # Dual-network (UPDATED)
    web3auth-provider.tsx.template  # Network-aware (UPDATED)
    tailwind-integra.ts.template    # Brand-aware (UPDATED)
    integra-config.json.template    # Project config (NEW)
  tools/                       # Existing tools directory
```

### Data Flow: Wizard Through Build

```
User runs /start
  |
  v
start/SKILL.md (AskUserQuestion loop)
  collects: network, branding, uiGeneration, category, idea, scope, name
  |
  v
Spawns 4 parallel subagents:
  architect        (reads: discovery brief, network config, branding choice)
  contract-designer (reads: discovery brief, conventions)
  frontend-designer (reads: discovery brief, branding file, Stitch tools if enabled)
  integration-planner (reads: discovery brief, ecosystem reference)
  |
  v
Writes project scaffold + docs/ + .integra/config.json
  |
  v
User runs /build
  |
  v
build/SKILL.md reads .integra/config.json
  |
  Phase 1: Contracts (executor reads contract-designer output)
  Phase 2: Frontend scaffold (executor reads frontend-designer output + network config)
  Phase 3: Contract integration (executor reads integration-planner output)
  Phase 4: UI Polish (ui-pipeline skill runs 6 skills sequentially)
  Phase 5: XP + ecosystem (executor reads integration-planner output)
  Phase 6: Testing + review (reviewer agent)
```

### Config Propagation

The `.integra/config.json` file is the single project-level config that travels through the entire pipeline:

```json
{
  "network": "mainnet",
  "branding": "integra",
  "uiGeneration": "stitch",
  "stitchProjectId": "abc123",
  "category": "defi",
  "scope": "mvp",
  "name": "My dApp"
}
```

Every agent and skill reads this file first. It determines:
- Which `knowledge/networks/*.md` file to load
- Which `knowledge/design-systems/*.md` file to load
- Whether to call Stitch MCP tools
- Scope-based decisions (prototype skips some polish, full adds extra features)

---

## Anti-Patterns to Avoid

### Anti-Pattern 1: Config Duplication Across Files
**What:** Same chain ID, RPC URL, or token name appears in CLAUDE.md, knowledge files, templates, and agent prompts.
**Why bad:** Updates miss one location, agents get conflicting data, generated code has wrong values.
**Instead:** Single source per data type. Knowledge files for network config. Templates reference knowledge. Agents read knowledge files. CLAUDE.md says "read the knowledge file."

### Anti-Pattern 2: Stitch as a Mandatory Dependency
**What:** Build pipeline fails or degrades if Stitch MCP is not configured.
**Why bad:** Stitch requires Google account + API key. Many developers will not have this.
**Instead:** Stitch is purely additive. Every path works without it. Frontend-designer produces a complete design document either way.

### Anti-Pattern 3: Parallel Skills That Produce Conflicting Output
**What:** Running ui-ux-pro-max and taste-skill in parallel, then trying to merge.
**Why bad:** Both modify spacing, color weights, and hierarchy. Merge conflicts are semantic, not textual.
**Instead:** Sequential pipeline. Each skill refines the previous output.

### Anti-Pattern 4: Subagent Asking User Questions
**What:** A subagent (e.g., discovery agent running inside /start) tries to call AskUserQuestion.
**Why bad:** AskUserQuestion only works from the main context, not subagents. The call will fail silently or error.
**Instead:** Only the orchestrating skill (start/SKILL.md) calls AskUserQuestion. Subagents receive collected answers as input.

### Anti-Pattern 5: Monolithic CLAUDE.md
**What:** Putting all network config, design tokens, contract interfaces, and conventions in CLAUDE.md.
**Why bad:** CLAUDE.md is always loaded. A 500-line CLAUDE.md wastes context window on irrelevant data. Network config is only needed during build, not during brainstorm.
**Instead:** CLAUDE.md is lean (rules + routing). Knowledge files provide domain data on demand.

---

## Scalability Considerations

| Concern | Current (V1) | V2 | Future |
|---------|--------------|-----|--------|
| Networks | 1 (testnet) | 2 (mainnet + testnet) | N (custom EVM chains) |
| Branding | 1 (Integra) | 2 (Integra + custom) | N (brand marketplace) |
| UI generation | Manual only | Manual + Stitch | Manual + Stitch + Figma MCP + V0 |
| Skills pipeline | None | 6 sequential | Configurable pipeline per project |
| Context window | ~20K tokens in CLAUDE.md | ~5K CLAUDE.md + knowledge on demand | Dynamic loading based on phase |

The architecture supports adding a third network by adding `knowledge/networks/devnet.md` and updating the wizard's network question. No other files change.

The architecture supports adding Figma MCP alongside Stitch by adding it as another tool to `frontend-designer` and adding a third option to the wizard's UI generation question.

---

## Sources

- [Google Stitch MCP GitHub](https://github.com/davideast/stitch-mcp) -- tool surface, authentication, integration
- [Google Stitch MCP + Claude Code workflow](https://www.sotaaz.com/post/stitch-mcp-integration-en) -- end-to-end usage
- [Claude Code Skills Documentation](https://code.claude.com/docs/en/skills) -- skill structure, frontmatter, invocation
- [Claude Code Sub-Agents](https://code.claude.com/docs/en/sub-agents) -- subagent creation, limitations
- [Claude Code Skill Chaining](https://www.mindstudio.ai/blog/claude-code-skill-collaboration-chaining-workflows) -- sequential pipeline patterns
- [Claude Code Sub-Agent Patterns](https://claudefa.st/blog/guide/agents/sub-agent-best-practices) -- parallel vs sequential
- [AskUserQuestion Tool Guide](https://smartscope.blog/en/generative-ai/claude/claude-code-askuserquestion-tool-guide/) -- MCQ patterns, timeout, limitations
- [Claude Code Dynamic Context Injection](https://www.365iwebdesign.co.uk/news/2026/01/23/claude-code-dynamic-context-injection/) -- conditional context loading
- [Claude Code Agent Teams](https://code.claude.com/docs/en/agent-teams) -- multi-session coordination
