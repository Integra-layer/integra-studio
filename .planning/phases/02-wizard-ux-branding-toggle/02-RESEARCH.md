# Phase 2: Wizard UX & Branding Toggle - Research

**Researched:** 2026-04-09
**Domain:** Claude Code skill UX patterns, design system architecture, AskUserQuestion constraints
**Confidence:** HIGH

## Summary

Phase 2 converts all technical configuration questions across 7 skills and 1 agent into selectable AskUserQuestion prompts, while keeping person-first discovery open-ended. The critical architectural constraint is that AskUserQuestion **cannot be called from subagents** (GitHub issue #29547) -- all user-facing questions must live in top-level skills, never in agents spawned via the Agent tool. This means the discovery agent's role must change from "interactive questionnaire" to "context analyzer" that receives pre-collected answers.

The branding toggle introduces a binary choice early in the `/start` wizard: Integra official branding vs custom design. Architecturally, this means maintaining two separate design system knowledge files (`integra-brand.md` and `custom-brand.md`) rather than conditional sections in one file. The official brand uses Coral `#FF6D49` and Euclid Circular B; custom triggers the `ui-ux-pro-max` skill pipeline. Both paths must produce the same output format: CSS variables + Tailwind config.

**Primary recommendation:** Audit every question across all 7 skills + discovery agent, classify each as open-ended (keep) or technical (convert to selectable), then rewrite skill files with the new question format. Branding toggle and network selector become new wizard steps in `/start` between discovery and synthesis phases.

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|------------------|
| WIZ-01 | All technical config questions use AskUserQuestion with 2-4 selectable options | Full question audit below; AskUserQuestion pattern documented in Architecture research |
| WIZ-02 | Free-text always as last option ("Let me describe..." / "Other") | Pattern: numbered options + last option is always free-text escape hatch |
| WIZ-03 | No open-ended questions for configuration -- only for ideation/discovery phase | Classification table below separates ideation (open) from config (selectable) |
| WIZ-04 | Network selection step early in `/start` wizard | Network selector UX pattern documented; goes after discovery, before synthesis |
| WIZ-05 | Branding toggle -- "Use Integra official branding or custom design?" | Two-file architecture for design systems; binary choice early in wizard |
| WIZ-06 | UI generation method -- "Generate UI with Stitch AI or build manually?" | Stitch availability check required before offering option; fallback to manual |
| WIZ-07 | Hybrid approach: open-ended for person-first discovery, selectable for all technical decisions | Question classification table identifies exactly which questions stay open vs convert |
| WIZ-08 | Update discovery agent to use selectable options for category/complexity/features | Discovery agent becomes non-interactive context analyzer; its questions move to `/start` skill |
| BRAND-01 | Integra branding loads official design tokens (Coral #FF6D49, Euclid Circular B) | Design system file `integra-brand.md` already exists with correct tokens |
| BRAND-02 | Custom design triggers ui-ux-pro-max to generate unique design system | Custom path invokes ui-ux-pro-max; output saved to `custom-brand.md` template |
| BRAND-03 | Both paths produce same output format: CSS variables + Tailwind config | Shared output format documented; both files use same section structure |
| BRAND-04 | Update design-system.md with latest brand assets from integra-brand repo | Current `knowledge/design-system.md` already has correct Coral palette + Euclid Circular B |
| BRAND-05 | Forbidden fonts list enforced: Inter, Montserrat, Space Grotesk, Kode Mono | Listed in `knowledge/design-system.md` line 106; must be in both brand files |
| BRAND-06 | Forbidden colors list enforced in Integra branding mode | Listed in `knowledge/design-system.md` line 75; enforcement = agent reads brand file |
</phase_requirements>

## Project Constraints (from CLAUDE.md)

These directives from CLAUDE.md constrain all implementation:

1. **AskUserQuestion is mandatory** for every question -- never output questions as plain text [CLAUDE.md line 160]
2. **MCQ format:** Include all options in the `question` parameter as a numbered list; user types their choice [CLAUDE.md line 163]
3. **Refinement loop required** before concluding ANY workflow [CLAUDE.md line 169]
4. **Font:** Euclid Circular B only (400/500/600/700) -- NEVER use Inter, Montserrat, or Space Grotesk [CLAUDE.md Design System]
5. **Dark theme by default.** Light theme supported but not required [CLAUDE.md Design System]
6. **Wallet:** `@integra/web3auth-provider` shared package -- never install Web3Auth directly [CLAUDE.md Frontend]
7. **Person-first:** Never show pre-made lists of ideas as the first question [CLAUDE.md Workflow]
8. **shadcn/ui** as base component library, customized to Integra palette [CLAUDE.md Frontend]
9. **CSS variables from `kit/brand-tokens.css`** -- never hardcode hex values [CLAUDE.md Design System]

**NOTE:** CLAUDE.md has a CONFLICT between root and `.claude/` versions. Root CLAUDE.md uses the correct Coral palette (#FF6D49, Euclid Circular B). `.claude/CLAUDE.md` still references the OLD purple palette (#6C5CE7, Inter font). Phase 2 must NOT touch `.claude/CLAUDE.md` -- that is Phase 1's CONFIG-02 responsibility. But skills and agents must reference `knowledge/design-system.md` (correct) rather than `.claude/CLAUDE.md` (outdated). [VERIFIED: codebase grep]

## Standard Stack

This phase modifies markdown skill/agent files and knowledge files. No npm packages are involved.

### Core Files to Modify

| File | Purpose | Change Type |
|------|---------|-------------|
| `skills/start/SKILL.md` | Main wizard -- owns all AskUserQuestion calls | Major rewrite: add network, branding, UI method selectors; restructure question flow |
| `skills/build/SKILL.md` | Build orchestrator | Minor: add selectable options for Phase 4 UI approach |
| `skills/deploy/SKILL.md` | Deployment | Minor: network selection already partially done; add selectable confirmation |
| `skills/review/SKILL.md` | Code review | Minor: selectable scope options |
| `skills/brainstorm/SKILL.md` | Idea generation | Minor: keep open-ended but add selectable follow-ups |
| `skills/research/SKILL.md` | Domain research | Already has selectable options (line 30-37); verify format |
| `skills/explore/SKILL.md` | Ecosystem explorer | Already has selectable menu; no changes needed |
| `agents/discovery.md` | Discovery agent | Major rewrite: remove all interactive questions, convert to context analyzer |
| `knowledge/design-system.md` | Current design system | Rename/move to `knowledge/design-systems/integra-brand.md` |
| `knowledge/design-systems/custom-brand.md` | Custom brand template | NEW file |

### No New Dependencies

This phase is purely markdown/documentation changes to skill files, agent definitions, and knowledge files. No npm packages, no code compilation, no runtime dependencies.

## Architecture Patterns

### Pattern 1: Selectable AskUserQuestion Format

**What:** Every technical question uses numbered options with a free-text escape hatch as the last option.
**When to use:** All configuration decisions (network, branding, UI method, scope, complexity, features).
**Never use for:** Person-first discovery questions (Q1-Q3 in start wizard).

```
AskUserQuestion(question: "Which network are you building for?\n\n1. Mainnet (production, real IRL tokens, Chain ID 26217)\n2. Testnet (development, free IRL from faucet, Chain ID 26218)\n3. Both (separate configs for each)")
```

Rules: [VERIFIED: CLAUDE.md + Architecture research]
- 2-4 options maximum per question (cognitive load research: 3-4 optimal)
- Last option is always free-text: "Let me describe..." / "Something else..." / "Other"
- Include one-line explanation for each option
- Mark recommended option with "(recommended)" label
- 60-second timeout -- keep questions concise

### Pattern 2: Hybrid Discovery Flow (Open-Ended Then Selectable)

**What:** The `/start` wizard has two distinct phases: open-ended discovery (person-first) followed by selectable configuration.
**Architecture:**

```
/start skill (owns ALL AskUserQuestion calls)
  |
  PHASE 1: UNDERSTAND THE PERSON (open-ended, 3-5 questions)
    Q1: "Tell me about yourself" -- open-ended
    Q2: Adaptive follow-up based on Q1 -- open-ended
    Q3: Connect to Integra capability -- open-ended
    Q4: Shape the idea (if needed) -- open-ended
    Q5: Scope + name -- semi-structured
  |
  PHASE 1.5: TECHNICAL CONFIGURATION (selectable, 3-4 questions)
    Q6: Network selection -- 3 options (mainnet/testnet/both)
    Q7: Branding -- 2 options (Integra official/custom design)
    Q8: UI generation -- 2-3 options (Stitch AI/manual/skip for now)
  |
  PHASE 2: SYNTHESIS
    Pass ALL answers to discovery agent (subagent, NO user interaction)
    Discovery agent produces Discovery Brief
    Show brief via AskUserQuestion: "Looks good? (yes / tweak / start over)"
```

[VERIFIED: Architecture research Section 4 + Pitfalls research Pitfall 4]

### Pattern 3: Non-Interactive Discovery Agent

**What:** The discovery agent stops being an interactive questionnaire and becomes a context analyzer.
**Why:** AskUserQuestion cannot be called from subagents (GitHub issue #29547). [VERIFIED: Pitfalls research]

Current discovery agent asks 8 questions interactively (category, idea, target users, features, complexity, mechanic, name, notes). In V2:
- Questions 1-5 (category, idea, target users, mechanic, notes) are covered by the start wizard's open-ended discovery phase
- Questions 6-8 (features, complexity, name) become selectable questions in the start wizard
- The discovery agent receives ALL collected answers and produces the Discovery Brief -- no user interaction

```yaml
# agents/discovery.md -- V2 role
---
name: discovery
description: Context analyzer -- produces Discovery Brief from wizard-collected answers
model: sonnet
tools: [Read, Grep, Glob]
---
# NO AskUserQuestion calls. Receives input, produces output.
```

### Pattern 4: Two-File Branding Architecture

**What:** Two separate design system knowledge files, one per branding path.
**Why:** Claude Code markdown files don't support conditional sections. Loading only the relevant file keeps agent context clean. [VERIFIED: Architecture research Section 5]

```
knowledge/
  design-systems/
    integra-brand.md     -- Official Integra branding (from current design-system.md)
    custom-brand.md      -- Template filled by ui-ux-pro-max during build
  design-system.md       -- REMOVED (replaced by the two files above)
```

The `.integra/config.json` written by the start wizard includes `"branding": "integra"` or `"branding": "custom"`. Downstream agents read this and load the corresponding file.

### Anti-Patterns to Avoid

- **Subagent calling AskUserQuestion:** Will silently return empty string. ALL user questions must be in top-level skills. [VERIFIED: Pitfalls Pitfall 4]
- **More than 4 options per question:** Causes decision fatigue. Keep to 2-3 when possible, 4 maximum. [VERIFIED: Pitfalls Pitfall 9]
- **Conditional sections in one design file:** Fragile; agent may mix tokens from both sections. Use separate files. [VERIFIED: Architecture research Section 5]
- **Hardcoding brand hex values in skills:** Skills reference `knowledge/design-systems/integra-brand.md` -- never inline hex codes. [VERIFIED: CLAUDE.md]

## Complete Question Audit

### `/start` Skill (skills/start/SKILL.md) -- MAJOR REWRITE

| # | Current Question | Type | V2 Action | Format |
|---|-----------------|------|-----------|--------|
| Q1 | "Tell me about yourself" | Open-ended | **KEEP** as-is | Free text |
| Q2 | Adaptive follow-up (pain point) | Open-ended | **KEEP** as-is | Free text |
| Q3 | "Interesting -- on Integra..." (spark) | Open-ended | **KEEP** as-is | Free text |
| Q4 | "If you had to explain in one sentence..." | Open-ended | **KEEP** as-is | Free text |
| Q5 | "How ambitious + what name?" | Mixed | **SPLIT** into 2 questions; scope becomes selectable | Scope: 3 options; Name: free text with 3 suggestions |
| NEW | Network selection | N/A | **ADD** after Q5 | 3 options: Mainnet / Testnet / Both |
| NEW | Branding toggle | N/A | **ADD** after network | 2 options: Integra official / Custom design |
| NEW | UI generation method | N/A | **ADD** after branding | 2-3 options: Stitch AI / Manual / Skip |
| Network Q | "Build for mainnet or testnet?" | Selectable | **MOVE** from after-discovery to Phase 1.5 | Already selectable (line 99-101) |
| Synthesis | "Does this capture it?" | Confirmation | **KEEP** as selectable | 3 options: yes / tweak / start over |
| Location | "Where should I create the project?" | Config | **CONVERT** to selectable | 2 options: default path / custom path |
| Refinement | "Anything to add, clarify, change?" | Open-ended | **KEEP** as-is (required by CLAUDE.md) | Free text |
| Research | "Want me to research?" | Config | **KEEP** as selectable | Already selectable |

### Discovery Agent (agents/discovery.md) -- MAJOR REWRITE

| # | Current Question | Type | V2 Action |
|---|-----------------|------|-----------|
| 1 | Category (9 options) | Selectable | **MOVE** to start wizard as selectable Q (but AFTER open-ended discovery, as a refinement) |
| 2 | Idea description | Open-ended | **REMOVE** -- covered by start wizard Q1-Q4 |
| 3 | Target users | Selectable | **MOVE** to start wizard or infer from discovery |
| 4 | Integra features | Selectable | **MOVE** to start wizard as selectable Q |
| 5 | Complexity | Selectable | **ALREADY** in start wizard Q5 as scope |
| 6 | Specific mechanic | Open-ended | **REMOVE** -- inferred from discovery conversation |
| 7 | Name | Open-ended | **ALREADY** in start wizard Q5 |
| 8 | Notes | Open-ended | **REMOVE** -- covered by refinement loop |

**V2 discovery agent role:** Receives all answers from start wizard, produces structured Discovery Brief. No AskUserQuestion calls.

### `/build` Skill (skills/build/SKILL.md) -- MINOR CHANGES

| # | Current Question | V2 Action |
|---|-----------------|-----------|
| Phase approval | "Ready to build Phase N?" | **CONVERT** to: "1. Yes, proceed / 2. Show me the plan first / 3. Skip this phase" |
| Decision points | "Which approach?" (e.g., proxy pattern) | **ALREADY** selectable -- verify format has numbered options |
| Iteration | "Proceed to frontend, or make changes?" | **CONVERT** to: "1. Proceed to Phase N+1 / 2. Make changes / 3. Review what was built" |
| Phase 4 UI | "Apply Integra design system" | **ADD** new question: read `.integra/config.json` branding choice and confirm approach |
| Refinement | "Anything to add, tweak, clarify?" | **KEEP** as-is (required) |

### `/deploy` Skill (skills/deploy/SKILL.md) -- MINOR CHANGES

| # | Current Question | V2 Action |
|---|-----------------|-----------|
| Network | Reads from config.json | **ADD** confirmation: "Deploying to [network]. Proceed? (1. Yes / 2. Switch to other network / 3. Cancel)" |
| Frontend deploy | Option A/B/C | **ALREADY** selectable -- verify format |
| Refinement | Post-deployment check | **KEEP** as-is |

### `/review` Skill (skills/review/SKILL.md) -- MINOR CHANGES

| # | Current Question | V2 Action |
|---|-----------------|-----------|
| Post-review | "Want to fix findings?" | **ALREADY** selectable (yes fix criticals / yes fix all / no) -- verify format |
| Refinement | "Anything else to check?" | **KEEP** as-is |

### `/brainstorm` Skill (skills/brainstorm/SKILL.md) -- MINIMAL CHANGES

| # | Current Question | V2 Action |
|---|-----------------|-----------|
| Step 1 | "What's on your mind?" | **KEEP** open-ended (ideation) |
| Step 2 | Follow-up | **KEEP** open-ended (ideation) |
| Step 3 | "Does that angle interest you?" | **CONVERT** to: "1. Yes, let's shape this / 2. Different direction / 3. More options" |
| Step 5 | Refinement | **KEEP** as-is |
| Step 6 | Research offer | **ALREADY** selectable |
| Step 7 | Transition | **ALREADY** selectable |

### `/research` Skill (skills/research/SKILL.md) -- NO CHANGES NEEDED

Already uses numbered selectable options (lines 30-37: 7 options for research scope). Format is correct. No changes needed.

### `/explore` Skill (skills/explore/SKILL.md) -- NO CHANGES NEEDED

Already uses numbered selectable menu (10 options + free-form). Format is correct. No changes needed.

### `/status` Skill (skills/status/SKILL.md) -- NO CHANGES NEEDED

Non-interactive (scan and report). No questions to convert.

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Design system format | Custom JSON schema for brand tokens | Markdown knowledge files with CSS variable tables | Claude Code agents read markdown natively; no parsing needed |
| Branding conditionals | `if/else` logic in markdown files | Two separate files, loaded by config flag | Markdown doesn't support conditionals; agents pick the right file |
| Question routing | Custom dispatcher to route questions to sub-skills | All questions in top-level skill, pass answers downstream | AskUserQuestion only works in top-level context |
| Option validation | Custom logic to validate user selections | Trust AskUserQuestion's built-in response handling | The tool handles input; skills interpret the text response |

## Common Pitfalls

### Pitfall 1: AskUserQuestion Silent Failure in Subagents
**What goes wrong:** Subagents (spawned via Agent tool) that call AskUserQuestion get empty string responses silently. No error thrown.
**Why it happens:** Claude Code limitation -- AskUserQuestion only works in top-level skill context. [VERIFIED: GitHub issue #29547]
**How to avoid:** ALL user-facing questions must be in the skill file (start/SKILL.md, build/SKILL.md, etc.), never in agent files (discovery.md, etc.).
**Warning signs:** Agent produces Discovery Brief with empty fields; wizard appears to skip questions.

### Pitfall 2: CLAUDE.md Design System Conflict
**What goes wrong:** `.claude/CLAUDE.md` still has the OLD purple palette (#6C5CE7, Inter font) while root `CLAUDE.md` and `knowledge/design-system.md` have the CORRECT Coral palette (#FF6D49, Euclid Circular B).
**Why it happens:** Phase 1 (CONFIG-02) hasn't run yet to sync these files.
**How to avoid:** Phase 2 skills/agents must reference `knowledge/design-system.md` or `knowledge/design-systems/integra-brand.md` for brand data -- NEVER reference `.claude/CLAUDE.md` for design tokens.
**Warning signs:** Generated projects use purple instead of coral, or Inter instead of Euclid Circular B.

### Pitfall 3: Option Overload in Wizard
**What goes wrong:** Converting every discovery question to selectable creates too many steps. A 12-question wizard with 4 options each overwhelms users.
**Why it happens:** Literal interpretation of WIZ-01 ("ALL technical config questions").
**How to avoid:** Keep person-first discovery open-ended (Q1-Q4). Only convert TECHNICAL CONFIG to selectable (network, branding, UI method, scope). Total selectable questions in start wizard should be 4-5, not 12.
**Warning signs:** Users abandon the wizard or take >60 seconds per question.

### Pitfall 4: Branding File Not Found
**What goes wrong:** Build skill tries to load `knowledge/design-systems/integra-brand.md` but the file is still at `knowledge/design-system.md` (old path).
**Why it happens:** Renaming the file without updating all references.
**How to avoid:** Phase 2 must update ALL references across all skills and agents when moving/renaming the design system file. Grep for `design-system.md` across the entire codebase.
**Warning signs:** Agent says "could not find design system" or falls back to default styles.

### Pitfall 5: Discovery Agent Still Has Interactive Questions
**What goes wrong:** After rewriting, the discovery agent still contains AskUserQuestion calls that worked in V1 (when it ran standalone) but fail in V2 (when spawned as subagent from /start).
**Why it happens:** Incomplete rewrite -- some question paths left in the agent file.
**How to avoid:** The V2 discovery agent must have ZERO AskUserQuestion calls. Grep the rewritten file for "AskUserQuestion" -- count must be 0.
**Warning signs:** Discovery Brief has empty/default values for fields that should have been filled.

## Code Examples

### Selectable Network Question (for start/SKILL.md)

```markdown
Use AskUserQuestion:

"Which network are you building for?

1. Mainnet (production-ready, Chain ID 26217, real IRL tokens)
2. Testnet (development, Chain ID 26218, free IRL from faucet) (recommended)
3. Both (separate configs, deploy to either)"
```
Source: [VERIFIED: adapted from .planning/research/FEATURES.md line 106]

### Selectable Branding Question (for start/SKILL.md)

```markdown
Use AskUserQuestion:

"Design approach for your dApp?

1. Integra official branding (Coral palette, Euclid Circular B font, dark theme)
2. Custom design (AI generates a unique look based on your concept)"
```
Source: [VERIFIED: adapted from .planning/research/FEATURES.md line 123]

### Selectable UI Generation Question (for start/SKILL.md)

```markdown
Use AskUserQuestion:

"How should we build the UI?

1. Generate with Stitch AI (Google's design tool creates screens, we refine)
2. Build manually (component-by-component with shadcn/ui)
3. Decide later (skip for now, choose during build phase)"
```
Source: [VERIFIED: adapted from .planning/research/ARCHITECTURE.md Section 4]

### Non-Interactive Discovery Agent Output Format

```markdown
## Discovery Brief

**Generated from wizard answers -- no user interaction in this agent**

- **Background**: {from Q1}
- **Pain Point**: {from Q2}
- **Spark/Direction**: {from Q3}
- **Idea**: {from Q3-Q4 synthesis}
- **Scope**: {from Q5 -- Prototype/MVP/Full}
- **Name**: {from Q5}
- **Network**: {from Q6 -- mainnet/testnet/both}
- **Branding**: {from Q7 -- integra/custom}
- **UI Method**: {from Q8 -- stitch/manual/deferred}
- **Integra Features**: {auto-selected based on idea analysis}
- **Complexity**: {inferred from scope + idea}
- **Target Users**: {inferred from Q1-Q4}
```
Source: [VERIFIED: adapted from agents/discovery.md output format + Architecture research Section 4]

### .integra/config.json Structure

```json
{
  "network": "testnet",
  "branding": "integra",
  "uiGeneration": "manual",
  "category": "defi",
  "scope": "mvp",
  "name": "My dApp"
}
```
Source: [VERIFIED: .planning/research/ARCHITECTURE.md Section 5 line 347-354]

## Validation Architecture

### Test Framework
| Property | Value |
|----------|-------|
| Framework | Manual verification (markdown files, no compiled code) |
| Config file | N/A |
| Quick run command | `grep -r "AskUserQuestion" skills/ agents/ \| wc -l` |
| Full suite command | See verification checklist below |

### Phase Requirements to Test Map
| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| WIZ-01 | All tech config questions are selectable | manual grep | `grep -c "1\." skills/start/SKILL.md` (count numbered options) | N/A |
| WIZ-02 | Free-text as last option | manual grep | `grep -c "Other\|describe\|Something else" skills/start/SKILL.md` | N/A |
| WIZ-03 | No open-ended config questions | manual review | Review start/SKILL.md Phase 1.5 section | N/A |
| WIZ-04 | Network selection in wizard | manual grep | `grep "network\|mainnet\|testnet" skills/start/SKILL.md` | N/A |
| WIZ-05 | Branding toggle in wizard | manual grep | `grep "branding\|official\|custom" skills/start/SKILL.md` | N/A |
| WIZ-06 | UI generation method in wizard | manual grep | `grep "Stitch\|manual\|generate" skills/start/SKILL.md` | N/A |
| WIZ-07 | Hybrid: open for discovery, selectable for config | manual review | Check Phase 1 is open-ended, Phase 1.5 is selectable | N/A |
| WIZ-08 | Discovery agent uses selectable (moved to skill) | manual grep | `grep -c "AskUserQuestion" agents/discovery.md` (must be 0) | N/A |
| BRAND-01 | Official tokens loaded | file check | `test -f knowledge/design-systems/integra-brand.md` | Wave 0 |
| BRAND-02 | Custom triggers ui-ux-pro-max | manual grep | `grep "ui-ux-pro-max" skills/start/SKILL.md` | N/A |
| BRAND-03 | Same output format | manual review | Compare section headings in both brand files | N/A |
| BRAND-04 | Latest brand assets | manual review | Compare integra-brand.md with knowledge/design-system.md | N/A |
| BRAND-05 | Forbidden fonts enforced | manual grep | `grep "Forbidden Fonts" knowledge/design-systems/integra-brand.md` | Wave 0 |
| BRAND-06 | Forbidden colors enforced | manual grep | `grep "Forbidden Colors" knowledge/design-systems/integra-brand.md` | Wave 0 |

### Sampling Rate
- **Per task commit:** `grep -r "AskUserQuestion" skills/ agents/ --include="*.md"` -- verify no regressions
- **Per wave merge:** Full checklist: every requirement manually verified against file content
- **Phase gate:** All 14 requirements pass verification before `/gsd-verify-work`

### Wave 0 Gaps
- [ ] `knowledge/design-systems/integra-brand.md` -- copy from `knowledge/design-system.md`, verify Coral palette
- [ ] `knowledge/design-systems/custom-brand.md` -- new template file with placeholder sections

## Security Domain

### Applicable ASVS Categories

| ASVS Category | Applies | Standard Control |
|---------------|---------|-----------------|
| V2 Authentication | no | N/A -- no auth in markdown skill files |
| V3 Session Management | no | N/A |
| V4 Access Control | no | N/A |
| V5 Input Validation | yes (indirect) | AskUserQuestion options constrain input; free-text escape hatch needs downstream validation |
| V6 Cryptography | no | N/A |

### Known Threat Patterns

| Pattern | STRIDE | Standard Mitigation |
|---------|--------|---------------------|
| User selects "Other" and provides malicious input | Tampering | Downstream agents must validate free-text answers; never inject raw user text into file paths or commands |
| Config.json tampering | Tampering | `.integra/config.json` values must be validated by build/deploy skills (only accept known enum values for network, branding, uiGeneration) |

This phase is LOW security risk -- it modifies markdown instruction files, not executable code. The generated projects' security is handled by Phase 1 (network config) and the build/review skills.

## Assumptions Log

| # | Claim | Section | Risk if Wrong |
|---|-------|---------|---------------|
| A1 | AskUserQuestion 60-second timeout is still current | Architecture Patterns | Low -- questions are short regardless |
| A2 | `ui-ux-pro-max` skill can generate a complete design system from text input | BRAND-02 support | Medium -- if it can't, custom branding path needs redesign |
| A3 | Phase 1 will complete before Phase 2 runs (network knowledge files exist) | Pattern 2 | High -- if Phase 1 hasn't created `knowledge/networks/`, network selector references break. Mitigation: Phase 2 can reference existing CLAUDE.md network data as fallback |
| A4 | `.integra/config.json` is the agreed config propagation mechanism | Code Examples | Medium -- if a different mechanism is chosen in Phase 1, Phase 2 must adapt |

## Open Questions (RESOLVED)

1. **Phase 1 dependency: Do network knowledge files exist yet?**
   - RESOLVED: Phase 1 is complete. `knowledge/networks/mainnet.md` and `testnet.md` exist. Plans hardcode chain IDs in wizard question text as backup.

2. **Stitch availability detection**
   - RESOLVED: Wizard always shows the Stitch option. Availability checked during build phase, not wizard. "Decide later" is option 3.

3. **Discovery agent: completely non-interactive or partially interactive?**
   - RESOLVED: Fully non-interactive. Plan 02-01 Task 2 converts discovery agent to a context analyzer with zero AskUserQuestion calls. Start wizard owns all user interaction.

## Sources

### Primary (HIGH confidence)
- Codebase analysis: all 7 skill files, discovery agent, knowledge/design-system.md [VERIFIED: direct file reads]
- `.planning/research/ARCHITECTURE.md` -- Sections 4 (Selectable Wizard) and 5 (Branding Toggle) [VERIFIED: file read]
- `.planning/research/PITFALLS.md` -- Pitfall 4 (AskUserQuestion silent failure), Pitfall 9 (option overload) [VERIFIED: file read]
- `.planning/research/FEATURES.md` -- Wizard UX Recommendations, Branding Toggle Pattern [VERIFIED: file read]
- `CLAUDE.md` (root) -- behavioral rules, design system, workflow philosophy [VERIFIED: file read]

### Secondary (MEDIUM confidence)
- [Claude Code AskUserQuestion Silent Failure (GitHub #29547)](https://github.com/anthropics/claude-code/issues/29547) -- referenced in pitfalls research [CITED: .planning/research/PITFALLS.md]
- [NNGroup Progressive Disclosure](https://www.nngroup.com/articles/progressive-disclosure/) -- UX research on staged wizard flow [CITED: .planning/research/FEATURES.md]

### Tertiary (LOW confidence)
- AskUserQuestion 60-second timeout claim [ASSUMED -- from architecture research, not independently verified]

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH -- all files identified via direct codebase analysis
- Architecture: HIGH -- patterns verified against existing research + codebase constraints
- Pitfalls: HIGH -- critical AskUserQuestion limitation verified via multiple sources
- Question audit: HIGH -- every question in every skill file was read and classified

**Research date:** 2026-04-09
**Valid until:** 2026-05-09 (stable -- markdown files, no version dependencies)
