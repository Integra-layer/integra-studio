---
phase: 02-wizard-ux-branding-toggle
plan: 02
subsystem: skills
tags: [ux, selectable-questions, branding-toggle, wizard]
dependency_graph:
  requires: []
  provides: [selectable-build-questions, selectable-deploy-questions, selectable-brainstorm-questions, selectable-review-questions, branding-aware-phase4]
  affects: [skills/build/SKILL.md, skills/deploy/SKILL.md, skills/brainstorm/SKILL.md, skills/review/SKILL.md]
tech_stack:
  added: []
  patterns: [numbered-selectable-options, branding-config-awareness]
key_files:
  created: []
  modified:
    - skills/build/SKILL.md
    - skills/deploy/SKILL.md
    - skills/brainstorm/SKILL.md
    - skills/review/SKILL.md
decisions:
  - Kept brainstorm Steps 1-2 open-ended (ideation/discovery per WIZ-03/WIZ-07)
  - Kept all refinement loops open-ended (per CLAUDE.md requirement)
  - Added free-text escape hatch on brainstorm Step 3 and deploy Step 5 (per WIZ-02)
metrics:
  duration_seconds: 101
  completed: 2026-04-09T10:55:53Z
  tasks_completed: 2
  tasks_total: 2
  files_modified: 4
---

# Phase 02 Plan 02: Selectable Questions for Build, Deploy, Brainstorm, Review Skills Summary

Converted all technical configuration questions across 4 skills to numbered selectable AskUserQuestion format; added branding-aware Phase 4 to build skill that reads .integra/config.json.

## Task Results

| Task | Name | Commit | Files |
|------|------|--------|-------|
| 1 | Convert build and deploy skill questions to selectable format | 131cb93 | skills/build/SKILL.md, skills/deploy/SKILL.md |
| 2 | Convert brainstorm and review skill questions to selectable format | 386eab9 | skills/brainstorm/SKILL.md, skills/review/SKILL.md |

## Changes Made

### Build Skill (skills/build/SKILL.md)
- Phase Execution Protocol Step 2 (approval): converted to 3-option selectable ("Yes, proceed" / "Show detailed plan" / "Skip this phase")
- Phase Execution Protocol Step 5 (iteration): converted to 3-option selectable ("Proceed to Phase N+1" / "Make changes" / "Review in detail")
- Phase 4 UI Polish: now reads `.integra/config.json` branding field, loads `integra-brand.md` or `custom-brand.md` accordingly
- Phase 4: added selectable design system confirmation (3 options)

### Deploy Skill (skills/deploy/SKILL.md)
- Added network confirmation after pre-flight checks: 3-option selectable ("Yes, deploy" / "Switch network" / "Cancel")
- Step 5 frontend deployment: converted from prose options to 4-option selectable with free-text last ("Docker" / "Static export" / "Vercel" / "Let me describe a different approach")

### Brainstorm Skill (skills/brainstorm/SKILL.md)
- Step 3 (Connect to Integra): converted to 3-option selectable with free-text escape ("Yes, shape into project" / "Different angle" / "Not quite -- let me describe")
- Step 6 (Optional Research): converted to 2-option selectable ("Yes, research" / "Skip")
- Step 7 (Transition): converted to 3-option selectable ("Yes, start design wizard" / "Keep exploring" / "Save for later")
- Steps 1-2 preserved as open-ended (ideation, not config)

### Review Skill (skills/review/SKILL.md)
- Post-Review Step 1: converted from inline parenthetical to properly numbered 3-option format ("Fix criticals first" / "Fix all" / "I'll handle it")
- Step 4 Integra Compliance: updated design system reference from `knowledge/design-system.md` to branding-aware path (`knowledge/design-systems/integra-brand.md` or `custom-brand.md`)

## Deviations from Plan

None -- plan executed exactly as written.

## Self-Check: PASSED
