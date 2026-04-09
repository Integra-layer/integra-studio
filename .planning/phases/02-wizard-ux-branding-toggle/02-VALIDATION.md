# Phase 02: Wizard UX & Branding Toggle - Validation Strategy

**Created:** 2026-04-09
**Phase:** 02-wizard-ux-branding-toggle

## Test Framework

| Property | Value |
|----------|-------|
| Framework | Manual verification (markdown files, no compiled code) |
| Quick run | `grep -r "AskUserQuestion" skills/ agents/ \| wc -l` |
| Full suite | See verification checklist below |

## Requirements Verification Map

| Req ID | Behavior | Verification Command |
|--------|----------|---------------------|
| WIZ-01 | All tech config questions are selectable | `grep -c "1\." skills/start/SKILL.md` |
| WIZ-02 | Free-text as last option | `grep -c "Other\|describe\|Something else" skills/start/SKILL.md` |
| WIZ-03 | No open-ended config questions | Review start/SKILL.md Phase 1.5 section |
| WIZ-04 | Network selection in wizard | `grep "network\|mainnet\|testnet" skills/start/SKILL.md` |
| WIZ-05 | Branding toggle in wizard | `grep "branding\|official\|custom" skills/start/SKILL.md` |
| WIZ-06 | UI generation method in wizard | `grep "Stitch\|manual\|generate" skills/start/SKILL.md` |
| WIZ-07 | Hybrid: open discovery, selectable config | Check Phase 1 open-ended, Phase 1.5 selectable |
| WIZ-08 | Discovery agent non-interactive | `grep -c "AskUserQuestion" agents/discovery.md` (must be 0) |
| BRAND-01 | Official tokens loaded | `test -f knowledge/design-systems/integra-brand.md` |
| BRAND-02 | Custom triggers ui-ux-pro-max | `grep "ui-ux-pro-max" skills/start/SKILL.md` |
| BRAND-03 | Same output format | Compare section headings in both brand files |
| BRAND-04 | Latest brand assets | Compare integra-brand.md with design-system.md |
| BRAND-05 | Forbidden fonts enforced | `grep "Forbidden Fonts" knowledge/design-systems/integra-brand.md` |
| BRAND-06 | Forbidden colors enforced | `grep "Forbidden Colors" knowledge/design-systems/integra-brand.md` |

## Sampling Rate

- **Per task commit:** `grep -r "AskUserQuestion" skills/ agents/ --include="*.md"` -- verify no regressions
- **Per wave merge:** Full checklist -- every requirement verified against file content
- **Phase gate:** All 14 requirements pass before `/gsd-verify-work`

## Wave 0 Gaps

- [ ] `knowledge/design-systems/integra-brand.md` -- created from `knowledge/design-system.md`
- [ ] `knowledge/design-systems/custom-brand.md` -- new template with placeholder sections
