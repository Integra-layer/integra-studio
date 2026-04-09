# Domain Pitfalls

**Domain:** Claude Code plugin for blockchain dApp generation (Integra Studio V2)
**Researched:** 2026-04-09

## Critical Pitfalls

Mistakes that cause rewrites, data loss, or fundamental breakage.

### Pitfall 1: Dual Decimal Precision — EVM 18 vs Cosmos 6

**What goes wrong:** Integra is a Cosmos SDK + EVM chain. The EVM layer uses 18 decimals (1 IRL = 10^18 airl), while the Cosmos SDK Bank module historically uses 6 decimals. A developer who sends 1 IRL via an EVM contract but reads the balance via a Cosmos query (or vice versa) will see a 10^12 magnitude discrepancy. In DeFi contexts, this causes catastrophic over/under-payment.

**Why it happens:** Ethereum tooling universally assumes 18 decimals. Cosmos tooling defaults to 6. When both are exposed on the same chain, any code that bridges between them without explicit conversion will silently produce wrong amounts.

**Consequences:**
- Token transfers that are 10^12x too large or too small
- DeFi calculations (collateral ratios, liquidation thresholds) that are off by orders of magnitude
- Users losing funds on mainnet with no recourse
- Precompiled contracts (Bank at `0x...0804`, Distribution at `0x...0801`) expecting 18 decimals while Cosmos CLI queries return 6

**Prevention:**
- Generated projects MUST include a `decimalUtils.ts` library that explicitly converts between EVM (18) and Cosmos (6) representations
- All contract templates must use `10**18` consistently and never assume 6 decimals
- The wizard should emit a prominent warning when the dApp touches native IRL transfers or interacts with precompiled contracts
- Add a lint rule or comment block in every generated hardhat config reminding developers about the dual-decimal reality
- Reference the `x/precisebank` module pattern if the chain implements it

**Detection:** Unit tests that assert token amounts across EVM and Cosmos boundaries. Any test that converts between `airl` and `IRL` display values.

**Confidence:** HIGH — documented in Cosmos EVM official docs and Evmos/Ethermint ecosystem. Integra's PROJECT.md explicitly flags this (NET-09).

---

### Pitfall 2: Wrong Network Deployment — Mainnet vs Testnet Config Swap

**What goes wrong:** With dual-network support (mainnet Chain ID 26217, testnet Chain ID 26218), a single digit difference in chain ID or a swapped RPC URL causes contracts to deploy to the wrong network. On mainnet, this spends real IRL. On testnet, it silently succeeds but the contract is unreachable from the intended network.

**Why it happens:**
- Chain IDs differ by 1 (26217 vs 26218, hex 0x6669 vs 0x666A) — easy to typo
- RPC URLs differ only by subdomain (`mainnet.integralayer.com` vs `testnet.integralayer.com`)
- `.env` files get copied between environments without updating all values
- Hardhat network names are arbitrary strings — `networks.mainnet` can point at any RPC
- Cosmos chain IDs follow a different format (`integra_26217-1` vs `integra-testnet-1`) adding a third ID to confuse

**Consequences:**
- Contracts deployed to mainnet with testnet assumptions (wrong gas price, wrong token addresses)
- WIRL token address (`0x5002...0001`) may only exist on one network
- Explorer links break (blockscout vs scan subdomain mismatch)
- Web3Auth configured for wrong network produces wallets that cannot transact

**Prevention:**
- Generated `hardhat.config.ts` must validate chain ID on connection before any deployment
- Add a `confirmNetwork()` pre-deployment script that logs chain ID, network name, and current balance, then requires explicit `--yes` flag for mainnet
- `.env.example` must have clearly separated sections: `# === MAINNET ===` and `# === TESTNET ===`
- Never default to mainnet in any generated config — always require explicit network selection
- Generated deployment scripts should check `chainId` matches expected value and abort on mismatch

**Detection:** A pre-deployment hook that prints `"You are about to deploy to MAINNET (26217). Gas price: 5 Twei. Continue? [y/N]"` and aborts without confirmation.

**Confidence:** HIGH — standard multi-network deployment issue, well-documented across Hardhat ecosystem.

---

### Pitfall 3: Web3Auth Network Mismatch — sapphire_devnet vs sapphire_mainnet

**What goes wrong:** Web3Auth generates entirely different wallet addresses depending on whether you configure `sapphire_devnet` or `sapphire_mainnet`. A user who logs in on devnet gets wallet address A; the same user on mainnet gets wallet address B. There is no migration path — the wallets are cryptographically distinct.

**Why it happens:**
- The Web3Auth Client ID is bound to a specific network at creation time in the Web3Auth Dashboard
- The `network` parameter in `Web3Auth()` constructor silently determines the key derivation path
- Developers test on `sapphire_devnet`, ship with the same Client ID, and users get empty wallets
- The verifier (Google, Email, X) must also be registered on the matching network

**Consequences:**
- Users lose access to all assets when the app switches networks
- Social login produces a wallet with no history on the new network
- No error is thrown — the app works perfectly with a different, empty wallet
- Support tickets from confused users who "lost everything"

**Prevention:**
- Generated `web3auth-provider.ts` must derive the Web3Auth network from the chain network config — if deploying to Integra mainnet, use `sapphire_mainnet`; if testnet, use `sapphire_devnet`
- Add a startup validation that checks: Client ID network matches configured `sapphire_*` network
- Document in generated `.env.example`: `# WARNING: Changing WEB3AUTH_NETWORK changes ALL user wallet addresses`
- The wizard should warn during network selection that Web3Auth network choice is permanent for production users
- Never hardcode `sapphire_devnet` in shared provider — always derive from environment

**Detection:** Integration test that initializes Web3Auth with both networks and asserts different addresses are returned for the same user credentials.

**Confidence:** HIGH — confirmed via Web3Auth official documentation and community reports of network mismatch errors.

---

### Pitfall 4: AskUserQuestion Silent Failure in Skill Chains

**What goes wrong:** When AskUserQuestion is called inside a skill that was invoked via the Skill tool from another skill, it silently auto-completes with an empty string. The user never sees the prompt. The skill proceeds with empty/default values, producing garbage output.

**Why it happens:** AskUserQuestion is not available in subagents spawned via the Agent tool. When skills chain — e.g., `/start` invokes a sub-skill that also needs user input — the inner skill's AskUserQuestion calls return immediately with empty answers. This is a known Claude Code limitation (GitHub issue #29547).

**Consequences:**
- Wizard appears to hang or skip questions
- Generated projects have empty/undefined configuration values
- User thinks the wizard crashed; restarts and loses progress
- The 6-skill UI pipeline (UISKILL-01 through UISKILL-07) cannot collect user preferences if chained

**Prevention:**
- All user-facing questions must live in the top-level skill, never in chained sub-skills
- Sub-skills should receive all needed parameters as input, not ask for them
- The skill pipeline (guidelines > pro-max > taste > shadcn > animation > frontend-design) must collect ALL configuration upfront in the `/start` wizard before dispatching to sub-skills
- Add a guard at the start of any skill that needs AskUserQuestion: check if running in subagent context and fail loudly rather than silently
- Design skills to be purely generative (take config in, produce output) rather than interactive

**Detection:** Test each skill both standalone and when invoked from another skill. If AskUserQuestion returns empty string, the skill should throw an error rather than continue.

**Confidence:** HIGH — confirmed via Claude Code GitHub issue #29547, directly impacts the V2 architecture.

---

### Pitfall 5: Precompiled Contract State Atomicity Failure

**What goes wrong:** Cosmos EVM precompiled contracts (staking at `0x...0800`, distribution at `0x...0801`, bank at `0x...0804`, etc.) are stateful — they modify Cosmos SDK state. If a precompile call partially executes and then reverts at the EVM level, the Cosmos state changes may not be reverted, leading to inconsistent state.

**Why it happens:** A critical vulnerability (GHSA-mjfq-3qr2-6g84) demonstrated that setting lower EVM call gas allows users to partially execute precompiles, causing state to be written without the full transaction completing. For example, claiming staking rewards via the distribution precompile could transfer funds without resetting the claimable balance.

**Consequences:**
- Double-claiming of staking rewards
- Funds transferred without corresponding state updates
- Broken invariants between EVM and Cosmos state
- Potential exploit vectors in any dApp that interacts with precompiles

**Prevention:**
- Generated contracts that call precompiles must use high gas limits (not `gasleft()` tricks)
- Warn developers in generated code comments: precompile calls are NOT like regular contract calls
- Add a `SafePrecompile.sol` library wrapper that enforces minimum gas for each precompile call
- The review skill must flag any direct `.call()` or `.staticcall()` to precompile addresses
- Stay updated on Cosmos EVM patches — this class of bug requires chain-level fixes

**Detection:** Fuzz tests that call precompiles with varying gas limits. Any test where gas is deliberately constrained near a precompile call.

**Confidence:** HIGH — documented CVE (GHSA-mjfq-3qr2-6g84), confirmed in Cosmos EVM security research by CertiK.

---

## Moderate Pitfalls

### Pitfall 6: Google Stitch HTML Export — Not Production-Ready

**What goes wrong:** Stitch generates visually appealing UI mockups but exports vanilla HTML/CSS with no interactivity, no component structure, and no framework bindings. Forms don't submit. Buttons don't navigate. The output looks like a screenshot, not an app.

**Why it happens:** Stitch is a design tool, not a code generator. It outputs static markup optimized for visual fidelity, not for React/Next.js component architecture. It has no concept of state management, event handlers, or routing.

**Prevention:**
- Treat Stitch output as a visual reference, not as code to integrate directly
- The build pipeline should: (1) generate with Stitch, (2) extract design tokens (colors, spacing, typography), (3) rebuild components in Next.js/shadcn using those tokens
- Never `dangerouslySetInnerHTML` Stitch output into a React component
- Document in the Stitch integration skill that the output is a design artifact, not a code artifact
- Generation limits (350 standard / 200 experimental per month) mean heavy iteration hits ceilings

**Detection:** Any generated component that contains raw HTML strings instead of React JSX.

**Confidence:** MEDIUM — based on multiple 2026 reviews confirming static-only export. Stitch is actively evolving (React export rumored for I/O 2026) so this may improve.

---

### Pitfall 7: shadcn/ui Theme Override Collisions

**What goes wrong:** shadcn/ui uses CSS custom properties (`--primary`, `--background`, `--foreground`, etc.) as its theming foundation. When Integra's brand system also defines `--primary` (Coral #FF6D49) and the developer runs `npx shadcn-ui@latest init`, it overwrites `globals.css` and wipes the brand tokens.

**Why it happens:**
- `shadcn init` regenerates `globals.css` with default HSL values
- Integra brand tokens use hex values; shadcn expects HSL format in CSS variables
- The Tailwind config `extend` block can conflict with shadcn's `plugins` array — `shadcn init` overwrites `tailwind.config.ts`
- Adding a `--brand` variable is the correct pattern, but generated code often reuses `--primary`

**Prevention:**
- Generated projects must run `shadcn init` FIRST, then overlay Integra brand tokens AFTER
- Brand tokens should use a distinct namespace: `--integra-primary`, `--integra-gold`, etc., mapped to shadcn's `--primary` via a single assignment in `globals.css`
- Include a `brand-tokens.css` that is imported after shadcn's base styles
- The build skill should never run `shadcn init` on an already-configured project
- Lock `globals.css` with a header comment: `/* INTEGRA BRAND TOKENS — DO NOT REGENERATE WITH shadcn init */`
- Every `--background` variable MUST have a matching `--foreground` for accessibility

**Detection:** Visual regression — dark theme text becomes unreadable because foreground wasn't set for a new background.

**Confidence:** HIGH — well-documented shadcn theming pattern, confirmed via official docs and troubleshooting guides.

---

### Pitfall 8: IAVL Tree Performance and Proof Incompatibility

**What goes wrong:** Integra stores EVM state in Cosmos IAVL trees instead of Ethereum's Merkle Patricia Trie. Any tooling that assumes MPT proofs (e.g., `eth_getProof`, light client verifiers, some bridge protocols) will fail or return incompatible data.

**Why it happens:** IAVL is the default state commitment structure in Cosmos SDK. When EVM state is stored in IAVL, the proof format differs from Ethereum's RLP-encoded MPT proofs. Tools like MetaMask's `eth_getProof` may return data in an unexpected format or fail entirely.

**Prevention:**
- Document in generated project README that `eth_getProof` may behave differently from mainnet Ethereum
- Avoid patterns that depend on Ethereum-style state proofs (e.g., Merkle proof verification in contracts)
- If building bridges or cross-chain verification, use IBC (ICS20 precompile at `0x...0802`) rather than MPT proofs
- Performance: IAVL has known write amplification issues — batch state changes where possible rather than individual writes
- IAVL hash is insertion-order-dependent, unlike MPT — do not assume proof stability across node restarts

**Detection:** Integration tests that call `eth_getProof` and verify the response format. Any test that constructs or verifies Merkle proofs.

**Confidence:** MEDIUM — architectural reality confirmed in Cosmos SDK docs. Specific impact on Integra needs chain-level testing.

---

### Pitfall 9: Wizard Option Overload and Analysis Paralysis

**What goes wrong:** The V2 wizard mandates selectable-only options (WIZ-01 through WIZ-06). If every question presents 4 options with sub-options, users face a combinatorial explosion. Research shows that for every 10 additional options offered, participation drops by 2%. A 10-step wizard with 4 options each = 4^10 theoretical paths = over 1 million combinations.

**Why it happens:**
- Designers want to cover every possibility
- AskUserQuestion supports 2-4 options, tempting developers to always use 4
- No progressive disclosure — all complexity shown upfront
- Missing smart defaults that skip questions for common paths

**Prevention:**
- Default to 2-3 options per question, not 4 — use 4 only when genuinely distinct
- Implement "opinionated defaults" path: for beginners, pre-select the recommended option and let them override
- Group related decisions: instead of 3 separate questions about network, wallet, and branding, show 2 profiles: "Production (mainnet + Web3Auth + Integra brand)" vs "Development (testnet + dev wallet + custom)"
- Add a progress indicator: "Step 3 of 7" so users know the scope
- Always include "Recommended" label on the safest option
- Provide an escape hatch: "Skip — use defaults for everything" as a fast path
- The "Other" / free-text option (WIZ-02) should be last and de-emphasized

**Detection:** User testing — if users take >60 seconds on any single question or abandon the wizard, there are too many options.

**Confidence:** HIGH — well-established UX research (Nielsen Norman Group, Paradox of Choice).

---

## Minor Pitfalls

### Pitfall 10: Mainnet Gas Price Misconfiguration

**What goes wrong:** Mainnet minimum gas price is 5,000,000,000,000 airl (5 Twei), which is much higher than typical Ethereum or testnet defaults. Generated Hardhat configs that omit `gasPrice` will fail with "insufficient gas price" on mainnet.

**Prevention:**
- Generated `hardhat.config.ts` must set `gasPrice: 5_000_000_000_000` for mainnet network
- Add a constant `MIN_GAS_PRICE_MAINNET` in generated utilities
- Testnet config should use a lower default but still document the mainnet requirement

**Confidence:** HIGH — specified in PROJECT.md (CONFIG-06).

---

### Pitfall 11: Explorer URL Mismatch

**What goes wrong:** Integra has TWO explorers: `blockscout.integralayer.com` for EVM transactions and `scan.integralayer.com` for Cosmos transactions. Generated "View on Explorer" links that point to the wrong explorer show "Transaction not found."

**Prevention:**
- EVM transactions (contract deploys, token transfers) link to Blockscout
- Cosmos transactions (staking, governance, IBC) link to Scan
- Generated frontend must detect transaction type and route to correct explorer
- Include a `getExplorerUrl(txHash, type: 'evm' | 'cosmos')` utility in every project

**Confidence:** HIGH — specified in PROJECT.md (CONFIG-04).

---

### Pitfall 12: Euclid Circular B Font Loading Failure

**What goes wrong:** Integra branding mandates Euclid Circular B (BRAND-03). This is a commercial font not available on Google Fonts or CDNs. If the font file is missing or fails to load, the browser falls back to system fonts, breaking brand consistency.

**Prevention:**
- Generated projects must include the font files in `public/fonts/` or use a self-hosted `@font-face` declaration
- Add a fallback chain: `"Euclid Circular B", "Inter", system-ui, sans-serif` — but only as fallback, never as primary in brand mode
- The build skill should verify font files exist before completing the branding step
- For custom (non-Integra) branding, Inter or any font is acceptable

**Confidence:** MEDIUM — font is specified in brand guidelines but loading mechanics need project-level verification.

---

### Pitfall 13: Google Stitch API Key and Account Dependency

**What goes wrong:** Stitch requires a Google account and `STITCH_API_KEY` env var. The MCP server (`@_davideast/stitch-mcp`) may not be installed. If either is missing, the UI generation step fails silently or throws an unhelpful error.

**Prevention:**
- The wizard must check for Stitch availability BEFORE offering the "Generate UI with Stitch AI" option (WIZ-06)
- If Stitch is unavailable, hide the option rather than showing it and failing later
- Generated `.env.example` must include `STITCH_API_KEY=` with a comment linking to the setup guide
- Stitch is experimental Google Labs product — document the risk that it may be deprecated

**Confidence:** MEDIUM — Stitch is free and actively developed but experimental. Google has a history of sunsetting Labs products.

---

## Phase-Specific Warnings

| Phase Topic | Likely Pitfall | Mitigation |
|-------------|---------------|------------|
| Dual-network config (NET-01 to NET-09) | Wrong chain ID, RPC swap, decimal confusion | Pre-deployment validation scripts, decimal conversion library |
| Google Stitch integration (STITCH-01 to STITCH-05) | Static HTML treated as production code, API key missing | Treat as design reference only, check availability before offering |
| UI skill pipeline (UISKILL-01 to UISKILL-07) | AskUserQuestion fails in chained skills | Collect all config upfront, pass to sub-skills as parameters |
| Wizard redesign (WIZ-01 to WIZ-06) | Option overload, missing escape hatches | 2-3 options default, smart profiles, progress indicators |
| Brand system update (BRAND-01 to BRAND-03) | shadcn init overwrites brand tokens, font loading failure | Namespace brand tokens, init shadcn first then overlay |
| Config updates (CONFIG-01 to CONFIG-06) | Gas price too low for mainnet, wrong explorer URL | Hardcode mainnet gas minimum, explorer URL router |
| Precompile interaction (NET-07) | Partial state writes, atomicity violation | SafePrecompile wrapper, minimum gas enforcement |

## Sources

- [Cosmos IAVL Repository](https://github.com/cosmos/iavl)
- [Cosmos SDK ADR-040: Storage and SMT State Commitments](https://github.com/cosmos/cosmos-sdk/blob/main/docs/architecture/adr-040-storage-and-smt-state-commitments.md)
- [Cosmos EVM Precompiled Contracts Documentation](https://docs.cosmos.network/evm/next/documentation/smart-contracts/precompiles)
- [Cosmos EVM Partial Precompile State Writes CVE (GHSA-mjfq-3qr2-6g84)](https://www.miggo.io/vulnerability-database/cve/GHSA-mjfq-3qr2-6g84)
- [CertiK: EVM-Cosmos Convergence Security Research](https://www.certik.com/resources/blog/evm-cosmos-convergence-research-from-security-base-part-2)
- [Cosmos EVM: Adding EVM to Existing Chain — Decimal Migration](https://docs.cosmos.network/evm/next/documentation/migrations/add-evm-to-existing-chain)
- [Web3Auth: Going Live Documentation](https://web3auth.io/docs/going-live)
- [Web3Auth Community: Network Mismatch Error](https://web3auth.io/community/t/network-mismatch-provided-network-testnet-does-not-match-project-network-sapphire-devnet/10261)
- [Claude Code: AskUserQuestion Silent Failure in Skills (Issue #29547)](https://github.com/anthropics/claude-code/issues/29547)
- [Claude Code: AskUserQuestion Bug in Plan Mode (Issue #9846)](https://github.com/anthropics/claude-code/issues/9846)
- [Google Stitch Review 2026 — Index.dev](https://www.index.dev/blog/google-stitch-ai-review-for-ui-designers)
- [Google Stitch Review 2026 — EduranceHub](https://edurancehub.co.in/google-stitch-review/)
- [Google Developers Blog: Introducing Stitch](https://developers.googleblog.com/stitch-a-new-way-to-design-uis/)
- [shadcn/ui Theming Documentation](https://ui.shadcn.com/docs/theming)
- [shadcn/ui Troubleshooting: Style Conflicts](https://eastondev.com/blog/en/posts/dev/20260402-shadcn-ui-troubleshooting/)
- [Customizing shadcn/ui Themes Without Breaking Updates](https://medium.com/@sureshdotariya/customizing-shadcn-ui-themes-without-breaking-updates-a3140726ca1e)
- [Hardhat 3: Multichain Support](https://hardhat.org/docs/explanations/multichain-support)
- [Nielsen Norman Group: Choice Overload](https://www.nngroup.com/videos/choice-overload/)
- [Wizard UI Design Best Practices — Lollypop Design](https://lollypop.design/blog/2026/january/wizard-ui-design/)
