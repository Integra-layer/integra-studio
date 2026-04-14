---
name: research
description: Research best practices, patterns, and pitfalls for your dApp before building. Optional but recommended.
user_invocable: true
trigger: "integra-studio:research"
---

# Research Skill

Investigate technical domain, existing implementations, security patterns, and best practices before building. Inspired by GSD's research-then-plan methodology — research runs in a fresh context so findings are clean and focused.

## CRITICAL: Use AskUserQuestion for ALL interactions

**Every question and confirmation MUST use the `AskUserQuestion` tool.** Never output a question as plain text.

## When to Use

- After `/integra-studio:start` and before `/integra-studio:build`
- When exploring a new contract pattern or integration
- When the user wants to validate their approach before coding
- Anytime the user says "research", "investigate", "what's the best way to..."

## Research Protocol

### Step 1 — Scope

Read `docs/PRD.md` and `docs/ARCHITECTURE.md` if they exist. Then use AskUserQuestion:

"What should I research? Pick areas or describe what you want to know:

1. Contract patterns — existing open-source contracts similar to yours
2. Security — vulnerabilities and mitigations for your contract types
3. Gas optimization — efficiency patterns for your use case
4. Frontend patterns — UX/UI approaches for similar dApps
5. Integra ecosystem — how to best leverage Integra features
6. All of the above
7. Something specific: (describe)"

### Step 2 — Investigate

For each selected research area, launch research agents in parallel:

**Contract Patterns:**
- Search for battle-tested open-source implementations (OpenZeppelin, Uniswap, Aave patterns)
- Identify standard patterns vs. custom logic needed
- Note which patterns are proven vs. experimental
- Tag findings: `[VERIFIED: source]`, `[COMMUNITY: widely used]`, `[EXPERIMENTAL: limited adoption]`

**Security:**
- Reference Integra security audit findings from `knowledge-bank/01-integra/security/`
- Check known vulnerabilities for the contract types being used
- Identify attack vectors specific to the dApp's functionality
- Cross-reference with OWASP Smart Contract Top 10

**Gas Optimization:**
- Storage layout optimization for the contract's data structures
- Calldata vs memory usage patterns
- Batch operation opportunities
- EVM-specific optimizations for Integra's Cosmos SDK + EVM module

**Frontend Patterns:**
- Successful dApp UX patterns for similar use cases
- Web3Auth integration best practices
- Transaction UX (pending states, confirmations, errors)
- Mobile-responsive Web3 patterns
- Read `knowledge/best-practices/ui-ux-design.md` for component and state patterns
- Read `knowledge/best-practices/design-adaptation.md` for category-specific design psychology
- Read `knowledge/best-practices/onboarding-ux.md` for first-time user experience patterns
- Read `knowledge/best-practices/accessibility.md` for WCAG AA requirements

**Integra Ecosystem:**
- Which Integra products fit the dApp best and why
- Token integration: which tokens (IRL, tUSDI, WIRL) to use and how — reference `knowledge/networks/tokens.md`
- XP event design for maximum engagement
- Cross-product integration opportunities
- ERC-7943 compliance requirements if handling tokens

**Performance & Quality:**
- Read `knowledge/best-practices/performance.md` for optimization targets
- Read `knowledge/best-practices/quality-checklist.md` for the master quality checklist
- Identify performance-critical paths for the dApp category

### Step 3 — Compile Findings

Save research to `docs/RESEARCH.md` with this structure:

```markdown
# Research Report — {dApp Name}

## Standard Stack
Libraries and patterns verified for this use case:
- {library} — {why it fits} [VERIFIED: {source}]

## Architecture Patterns
Recommended approaches with trade-offs:
- Pattern A: {description} — Pro: {pro}, Con: {con}

## Security Considerations
Known risks and mitigations:
- Risk: {description} — Mitigation: {approach} [SOURCE: {reference}]

## Gas Optimization
Efficiency opportunities:
- {optimization} — Expected savings: {estimate}

## Don't Hand-Roll
Problems with proven solutions — never build custom:
- {problem} → Use {solution} instead

## Common Pitfalls
Mistakes to avoid:
- {pitfall} — Why: {explanation}

## Assumptions
Claims that need user validation before locking:
- [ASSUMED] {claim} — Confidence: LOW/MEDIUM
```

### Step 4 — Present and Discuss

Show the research summary. Then use AskUserQuestion:

"Here's what I found. Any questions, or areas you want me to dig deeper into?"

If the user wants more depth, investigate further. Repeat until satisfied.

### Step 5 — Refinement Loop

Use AskUserQuestion:

"Before we wrap up the research — anything else you'd like me to investigate? Any grey areas?"

Repeat until the user confirms they're done.

### Step 6 — Transition

Use AskUserQuestion:

"Research complete. Want to adjust your architecture based on these findings? Or ready to build? (adjust docs / start building / keep researching)"

If adjusting → update relevant docs in `docs/`
If building → suggest `/integra-studio:build`

## Research Quality Rules

- **Provenance matters.** Tag every claim with its source. Don't present assumptions as facts.
- **Verify negative claims.** "Couldn't find X" ≠ "X doesn't exist." Check official docs before concluding.
- **Prefer battle-tested over novel.** For security-critical code, proven patterns beat clever ones.
- **Quantify when possible.** "Saves ~30% gas" beats "more efficient."
- **Flag low-confidence claims.** Mark assumptions that need user validation with `[ASSUMED]`.
