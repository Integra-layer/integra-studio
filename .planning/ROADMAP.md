# Roadmap: Integra Studio V2

**Created:** 2026-04-09
**Milestone:** V2 — Major Overhaul
**Phases:** 5
**Estimated Plans:** 18-22

---

## Phase 1: Network Configuration & Knowledge Refactor
**Goal:** Single source of truth for all chain config. Every agent, template, and skill reads correct mainnet + testnet data from knowledge files.

**Requirements:** NET-01 to NET-14, CONFIG-01 to CONFIG-10

**Success Criteria:**
- [ ] `knowledge/networks/mainnet.md` exists with Chain ID 26217, all RPCs, gas price, precompiles
- [ ] `knowledge/networks/testnet.md` exists with Chain ID 26218, all RPCs, faucet info
- [ ] `knowledge/networks/shared.md` exists with IRL/airl, WIRL address, decimal warning, precompile table
- [ ] CLAUDE.md (root + .claude/) refactored — no inline network data, points to knowledge/networks/
- [ ] .claude/CLAUDE.md synchronized with root CLAUDE.md
- [ ] knowledge/integra-ecosystem.md cleaned — products/APIs only, no duplicate network config
- [ ] templates/hardhat.config.ts.template has both mainnet + testnet networks
- [ ] templates/env.example.template has INTEGRA_MAINNET_RPC_URL + INTEGRA_TESTNET_RPC_URL
- [ ] templates/web3auth-provider.tsx.template is network-aware (sapphire_mainnet vs sapphire_devnet)
- [ ] templates/tailwind-integra.ts.template has latest brand tokens (Coral #FF6D49)
- [ ] All 8 agents read from knowledge/networks/ instead of inline CLAUDE.md config
- [ ] Deploy skill supports `--network mainnet|testnet` target selection
- [ ] Old testnet-rpc.integralayer.com URL removed everywhere
- [ ] Faucet amount corrected to 10 IRL + 1,000 tUSDI per request

**Plans:** 4-5
- Plan 1: Create knowledge/networks/ files (mainnet.md, testnet.md, shared.md)
- Plan 2: Refactor CLAUDE.md files — extract network data, add network knowledge pointers
- Plan 3: Update all templates for dual-network support
- Plan 4: Update all 8 agents to reference knowledge/networks/
- Plan 5: Update deploy skill for mainnet/testnet target

---

## Phase 2: Wizard UX & Branding Toggle
**Goal:** Every technical question is selectable. Branding and network choices are first-class wizard steps. Person-first discovery stays open-ended.

**Depends on:** Phase 1 (network config must exist for network selector)

**Requirements:** WIZ-01 to WIZ-08, BRAND-01 to BRAND-06

**Success Criteria:**
- [ ] `/start` wizard asks "Build for mainnet or testnet?" with selectable options (default: mainnet)
- [ ] `/start` wizard asks "Use Integra official branding or custom design?" with selectable options
- [ ] `/start` wizard asks "Generate UI with Stitch AI or build manually?" with selectable options
- [ ] All technical config questions in all skills use AskUserQuestion with 2-4 options
- [ ] Free-text ("Other" / "Let me describe...") is always the last option on technical questions
- [ ] Discovery phase (understanding the person) remains open-ended per V1 philosophy
- [ ] Category/complexity/feature selections in discovery agent use selectable options
- [ ] Integra branding loads official design tokens from design-system.md
- [ ] Custom design triggers ui-ux-pro-max skill to generate unique design system
- [ ] Both branding paths produce CSS variables + Tailwind config in same format
- [ ] design-system.md updated with latest brand assets (Coral primary, Euclid Circular B)
- [ ] Forbidden fonts/colors lists enforced in knowledge files

**Plans:** 4-5
- Plan 1: Update start skill — add network selector, branding toggle, UI method selector
- Plan 2: Update discovery agent — selectable options for category/complexity/features
- Plan 3: Audit and convert all skills to selectable-only technical questions
- Plan 4: Implement branding toggle logic — official vs custom design system generation
- Plan 5: Update design-system.md with latest brand assets + forbidden lists

---

## Phase 3: Google Stitch Integration
**Goal:** Frontend-designer agent can generate AI-powered UI screens via Stitch MCP, with graceful fallback.

**Depends on:** Phase 2 (wizard must offer Stitch option, branding must be selected)

**Requirements:** STITCH-01 to STITCH-07

**Success Criteria:**
- [ ] `knowledge/stitch-integration.md` documents MCP setup, SDK usage, and pipeline
- [ ] Frontend-designer agent can invoke Stitch MCP tools to generate screens
- [ ] Stitch design system created from Integra brand tokens when official branding selected
- [ ] Stitch design system created from ui-ux-pro-max output when custom branding selected
- [ ] 1-3 variant options generated for user selection during build phase
- [ ] HTML export from Stitch serves as visual specification for executor agent
- [ ] Graceful fallback: if Stitch unavailable, falls back to manual UI skill pipeline
- [ ] STITCH_API_KEY documented in env.example template
- [ ] Build skill Phase 2 (Frontend Scaffold) checks for Stitch availability

**Plans:** 3-4
- Plan 1: Create knowledge/stitch-integration.md — setup guide, MCP config, API patterns
- Plan 2: Update frontend-designer agent — Stitch screen generation + variant workflow
- Plan 3: Update build skill — Stitch pipeline in Phase 2, fallback to manual
- Plan 4: Update env.example template with STITCH_API_KEY

---

## Phase 4: UI/UX Skill Pipeline
**Goal:** Every generated frontend passes through a multi-skill quality pipeline that produces premium, polished UI.

**Depends on:** Phase 2 (branding toggle determines pipeline variant), Phase 3 (Stitch output feeds into pipeline)

**Requirements:** UISKILL-01 to UISKILL-07

**Success Criteria:**
- [ ] Executor agent references all 6 UI/UX skills by name in its instructions
- [ ] Lean pipeline for Integra branding: frontend-design > shadcn > animation (skip pro-max)
- [ ] Full pipeline for custom branding: guidelines > pro-max > shadcn > animation > frontend-design
- [ ] ui-ux-pro-max wired into frontend-designer for design direction
- [ ] frontend-patterns skill referenced for component architecture
- [ ] react-dev skill referenced for React 18-19 patterns
- [ ] Build Phase 4 (UI Polish) runs the appropriate pipeline based on branding choice
- [ ] Generated components follow shadcn/ui patterns with correct theme tokens
- [ ] Animations follow ui-animation patterns (subtle transitions, 150-300ms)

**Plans:** 3
- Plan 1: Update executor agent instructions — reference all 6 skills, define pipeline order
- Plan 2: Update frontend-designer agent — invoke ui-ux-pro-max for design direction
- Plan 3: Update build skill Phase 4 — run lean or full pipeline based on branding choice

---

## Phase 5: Documentation & Polish
**Goal:** README, knowledge files, and overall coherence reflect the V2 upgrade.

**Depends on:** Phase 1-4

**Requirements:** DOC-01 to DOC-03

**Success Criteria:**
- [ ] README.md updated — reflects dual-network, Stitch, branding toggle, UI pipeline
- [ ] knowledge/stitch-integration.md comprehensive and accurate
- [ ] knowledge/ui-skill-pipeline.md documents lean vs full pipeline
- [ ] All cross-references between CLAUDE.md, agents, skills, and knowledge are consistent
- [ ] No stale references to old testnet-only config anywhere in the project
- [ ] All agent model assignments reviewed and correct

**Plans:** 2-3
- Plan 1: Update README.md with V2 features and architecture
- Plan 2: Create knowledge/ui-skill-pipeline.md — pipeline guide
- Plan 3: Cross-reference audit — verify consistency across all files

---

## Summary

| Phase | Name | Requirements | Plans | Depends On |
|-------|------|-------------|-------|------------|
| 1 | Network Config & Knowledge Refactor | NET-01..14, CONFIG-01..10 | 4-5 | -- |
| 2 | Wizard UX & Branding Toggle | WIZ-01..08, BRAND-01..06 | 4-5 | Phase 1 |
| 3 | Google Stitch Integration | STITCH-01..07 | 3-4 | Phase 2 |
| 4 | UI/UX Skill Pipeline | UISKILL-01..07 | 3 | Phase 2, 3 |
| 5 | Documentation & Polish | DOC-01..03 | 2-3 | Phase 1-4 |

**Total:** 5 phases, 16-20 plans, 52 requirements

---
*Roadmap created: 2026-04-09*
*Last updated: 2026-04-09 after requirements definition*
