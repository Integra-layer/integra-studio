---
name: brainstorm
description: Brainstorm dApp ideas for the Integra ecosystem. Explore possibilities, get inspired.
user_invocable: true
trigger: "integra-studio:brainstorm"
---

# Brainstorm Skill

Free-form brainstorming mode for dApp ideas on the Integra blockchain. Help the user discover what to build.

## CRITICAL: Use AskUserQuestion for ALL interactions

**Every question and prompt MUST use the `AskUserQuestion` tool.** Never output a question as plain text. Always use AskUserQuestion so the user gets a proper interactive prompt.

Examples:
- Initial: `AskUserQuestion(question: "What area interests you?\n\n1. DeFi\n2. AI Agents\n3. Social\n4. Gaming\n5. NFTs\n6. Infrastructure\n7. Surprise me")`
- Follow-up: `AskUserQuestion(question: "Any of these spark something? Pick a number, or say 'more' for fresh ideas.")`
- Transition: `AskUserQuestion(question: "Want to build this? I can run /integra-studio:start with this idea pre-filled. (yes / tweak it / more ideas)")`

## Activation

When this skill is invoked, enter brainstorming mode. The user either:
- Describes a vague idea ("I want to build something with NFTs")
- Asks for ideas ("give me ideas", "what should I build?")
- Wants to explore a category ("what's possible with the Wrapper?")

## Brainstorming Process

### Step 1: Context Gathering (if user has an idea)

If the user has a vague idea, ask 2-3 quick questions:
- What problem are you solving? Who is it for?
- Which Integra products interest you? (Asset Passport, Wrapper, GOB, Agent Arena, XP)
- How complex should it be? (weekend project / serious product / experiment)

If the user just wants ideas, skip to Step 2.

### Step 2: Generate Idea Cards

Present exactly 5 idea cards. Each card must include:

```
[N] NAME
    One-liner description (max 15 words)
    Category: DeFi | NFT | Gaming | Social | DAO | AI | Infrastructure | Commerce
    Complexity: Weekend (1-2 days) | Sprint (1-2 weeks) | Project (1+ month)
    Integra Features: [list which Integra products it uses]
    Why it's interesting: [1 sentence on what makes this unique to Integra]
```

### Idea Generation Guidelines

When generating ideas, consider:

**What's missing in the Integra ecosystem:**
- Asset Passport needs dApps that CREATE and TRADE passported assets
- The Wrapper needs frontends for wrapping/unwrapping flows
- GOB needs specialized marketplaces and trading interfaces
- Agent Arena needs agent builders and dashboards
- XP system needs gamified experiences that reward participation

**What complements existing products:**
- Analytics dashboards for on-chain activity
- Portfolio trackers for wrapped assets
- Social features around asset collections
- Governance tools for community decisions
- Bridge UIs for cross-chain asset movement

**What's trending in Web3/AI that fits Integra:**
- AI agent marketplaces (Agent Arena native)
- RWA tokenization frontends (Asset Passport native)
- Prediction markets on real-world events
- Decentralized identity and reputation systems
- On-chain attestation and verification
- Automated market makers for niche assets
- Creator economies with on-chain royalties

### Step 3: User Selection

After showing the 5 cards, ask:
- "Pick a number to explore deeper, or say 'more' for 5 new ideas."
- "You can also mix ideas — e.g., 'combine 2 and 4'."

If user says "more": Generate 5 NEW ideas (don't repeat). You can go up to 3 rounds (15 ideas total). After that, recommend picking from the best ones shown.

### Step 4: Deep Dive

When the user picks an idea, expand it into a mini-brief:

```
NAME — Deep Dive

Problem: What specific problem this solves
Users: Who would use this and why
Core Features:
  1. Feature one (maps to Integra product X)
  2. Feature two (maps to Integra product Y)
  3. Feature three (unique to this dApp)
Technical Sketch:
  - Contracts needed: [list]
  - Frontend pages: [list]
  - Integra integrations: [list]
Revenue Model: How this could generate value (if applicable)
Differentiation: Why build on Integra vs other chains
```

### Step 5: Transition to Build

After the deep dive, ask:
"Want to build this? I can run `/integra-studio:start` to set up the full project."

If user says yes, invoke the start skill with the idea context pre-filled:
- Pass the idea name, description, core features, and technical sketch
- The start skill's discovery agent should use this as a starting point instead of asking from scratch

## Tone

Be creative and enthusiastic but grounded. Don't oversell — be honest about complexity. If an idea is a weekend project, say so. If it needs serious infrastructure, say that too. Prefer ideas that are buildable NOW with the tools available.

## Knowledge Reference

Read `knowledge/integra-ecosystem.md` for available contract interfaces, APIs, and integration patterns. Use real function signatures and endpoints in the technical sketches — don't make up APIs that don't exist.
