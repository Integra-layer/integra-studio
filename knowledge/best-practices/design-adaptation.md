# Design Adaptation Guide

How to adapt colors, typography, layout, and visual language based on the dApp category. When using **custom branding**, the `ui-ux-pro-max` skill should use this guide to generate an appropriate design system. When using **Integra branding**, these adaptations apply as subtle variations within the official palette.

---

## 1. Design Psychology by Category

### DeFi (Lending, DEX, Yield, Staking)

**Mood:** Trust, precision, professional
**Why:** Users are handling real money. The UI must feel reliable and data-accurate.

| Aspect | Recommendation | Rationale |
|--------|---------------|-----------|
| Primary Color | Deep blue, teal, or cool green | Blue = trust, stability. Cool tones = financial seriousness |
| Accent | Gold, amber, or warm green | Gold = wealth, rewards. Green = profit, positive returns |
| Typography | Medium weight, clean sans-serif | Readability for numbers and data is critical |
| Layout | Data-dense, dashboard-style | Traders need information density, not whitespace |
| Cards | Sharp corners (8px), minimal glassmorphism | Precision feel. Too much blur = imprecise |
| Animations | Minimal — number counters, subtle fades | Data should feel real-time, not decorative |
| Charts | Candlestick green/red, line charts in brand color | Standard financial conventions users expect |
| Key Components | Price tickers, APY badges, position tables, swap forms | Function-first layout |

**Integra brand adaptation:** Use Teal (#00A186) as dominant accent with Coral for CTAs. Gold (#FFC17A) for reward/yield indicators.

**Token emphasis:** IRL/tUSDI trading pair prominently displayed. Show token balances in header.

### Gaming (Prediction, PvP, Collection)

**Mood:** Excitement, energy, fun
**Why:** Gaming UX should feel dynamic and rewarding. Dopamine-driven design.

| Aspect | Recommendation | Rationale |
|--------|---------------|-----------|
| Primary Color | Vibrant purple, electric blue, or neon accents | Energy, excitement, gaming culture |
| Accent | Hot pink, lime green, or orange | High-energy secondary colors for achievements |
| Typography | Bold weights (600-700), slightly larger scale | Games need punchy, readable text |
| Layout | Card-heavy, visual-first | Show game states, avatars, achievements visually |
| Cards | Rounded (16px), glowing borders on active items | Playful, interactive feel |
| Animations | More liberal — bounce, glow, pulse for achievements | Reward moments should feel celebratory |
| Key Components | Leaderboards, achievement badges, countdown timers, avatar displays | Engagement-first layout |

**Integra brand adaptation:** Use Pink (#F34499) as dominant accent. Brand gradient for hero sections. Coral for action buttons.

**Token emphasis:** IRL for entry fees/rewards. tUSDI for prize pools. Show XP prominently.

### NFT / Collectibles (Marketplace, Gallery, Minting)

**Mood:** Gallery, premium, artistic
**Why:** Art and collectibles need to shine. The UI is a frame for the content.

| Aspect | Recommendation | Rationale |
|--------|---------------|-----------|
| Primary Color | Neutral dark with one bold accent | Let the NFT artwork be the color, not the UI |
| Accent | Coral, gold, or single vibrant color | Sparse use — only for actions and highlights |
| Typography | Light/regular weights, elegant sizing | Gallery feel — not shouting, presenting |
| Layout | Grid-based, generous whitespace | Art needs breathing room. No cramped layouts |
| Cards | Large images, minimal text overlay | The NFT IS the card content |
| Animations | Subtle hover zoom (1.02 scale), image reveal | Elegant, not flashy. Treat items as precious |
| Key Components | Image grids, detail modals, bid forms, collection views | Content-first layout |

**Integra brand adaptation:** Coral for CTAs only. Dark surfaces dominate. Use gold (#FFC17A) for auction/bid elements.

**Token emphasis:** IRL for minting fees. tUSDI as optional payment method. Show floor price in both.

### Social (Profiles, Reputation, DAO, Messaging)

**Mood:** Warm, approachable, community
**Why:** Social apps need to feel human and inviting, not cold or technical.

| Aspect | Recommendation | Rationale |
|--------|---------------|-----------|
| Primary Color | Warm tones — coral, orange, or soft purple | Approachable, human, community feel |
| Accent | Complementary warm or cool contrast | Creates visual interest without coldness |
| Typography | Regular/medium weights, friendly sizing | Conversational feel, not corporate |
| Layout | Feed-style, profile-centric | Users are the content — center around people |
| Cards | Rounded (12-16px), soft shadows | Friendly, not sharp or institutional |
| Animations | Smooth transitions, like/react micro-animations | Social feedback should feel tactile |
| Key Components | User cards, feeds, voting UI, proposal cards, chat bubbles | Interaction-first layout |

**Integra brand adaptation:** Coral as primary. Pink for social actions (like, share). Warm gradient for profile headers.

**Token emphasis:** IRL for governance/staking. tUSDI for tipping/micropayments.

### AI Agents (Trading Bots, Managers, Analysts)

**Mood:** Futuristic, intelligent, sleek
**Why:** AI products should feel cutting-edge and data-driven.

| Aspect | Recommendation | Rationale |
|--------|---------------|-----------|
| Primary Color | Dark blue-black, cyberpunk accents | Futuristic, tech-forward |
| Accent | Cyan, electric blue, or neon green | AI/data aesthetic — glowing, digital |
| Typography | Monospace for data, clean sans for UI | Data/code feel for agent outputs |
| Layout | Split-panel: controls left, agent output right | Operator dashboard feel |
| Cards | Thin borders, dark surfaces, subtle glow on active | Clean, digital aesthetic |
| Animations | Typing indicators, data streaming effects, pulse on activity | Agents should feel "alive" |
| Key Components | Agent cards, activity logs, performance charts, config panels | Control-panel layout |

**Integra brand adaptation:** Dark surfaces with coral accents. Use Geist Mono for agent data. Pink gradient for "intelligence" indicators.

**Token emphasis:** IRL for agent deployment/gas. tUSDI for agent trading capital. Show agent P&L.

### Infrastructure (Oracle, Dashboard, SDK, Bridge)

**Mood:** Technical, reliable, developer-friendly
**Why:** Infrastructure users are developers. Clarity and documentation matter more than aesthetics.

| Aspect | Recommendation | Rationale |
|--------|---------------|-----------|
| Primary Color | Neutral with blue or green accent | Trust, stability, developer-familiar |
| Accent | Green for status, blue for info | Standard dev tool color conventions |
| Typography | Monospace prominent, clean sans for prose | Code-friendly display |
| Layout | Docs-style sidebar + content area | Developer documentation pattern |
| Cards | Minimal, code-block styled | Functional over decorative |
| Animations | Minimal — only for status transitions | Developers want speed, not effects |
| Key Components | Code blocks, API explorers, status dashboards, config forms | Documentation-first layout |

**Integra brand adaptation:** Coral for navigation accents. Teal for status/health indicators. Minimal gradient use.

**Token emphasis:** Show gas costs prominently. tUSDI for API credits if applicable.

---

## 2. Universal Design Rules (All Categories)

These apply regardless of category:

1. **Dark theme default** — light theme as optional toggle
2. **Loading states everywhere** — skeleton loaders matching content shape
3. **Error states are helpful** — tell users what went wrong AND what to do
4. **Empty states guide action** — never show a blank page
5. **Responsive at all breakpoints** — 375px, 640px, 1024px, 1440px
6. **WCAG AA contrast** — 4.5:1 for body text, 3:1 for large text
7. **Token balances visible** — show IRL + tUSDI balance in header/sidebar
8. **Network indicator** — always show testnet/mainnet badge
9. **Transaction feedback** — pending → submitted → confirmed → failed flow
10. **XP integration** — show XP earned on key actions, progress toward next level

---

## 3. Color Pairing Rationale

When generating custom palettes, explain the reasoning:

```
"We chose [primary color] because [psychological reason].
The [accent color] creates [contrast/complement] that [specific UX benefit].
For status colors, we kept [success/warning/danger] because [universal recognition].
The dark surfaces use [specific hex] to [create depth/reduce eye strain/etc]."
```

Example for a DeFi app:
> "We chose deep teal (#0B6E5F) as the primary because teal signals trust and stability — essential for a platform handling user funds. The gold accent (#D4A843) naturally associates with wealth and rewards, making yield indicators feel premium. Status colors remain standard (green/amber/red) because in financial contexts, users must instantly recognize positive/negative states without learning new conventions."

---

## 4. Applying Adaptations

### During `/start` wizard
When the user's idea falls into a category, the integration planner agent should note the recommended design adaptation in `docs/FRONTEND.md`.

### During `/build` Phase 4 (UI Polish)
- If **Integra branding**: Apply the "Integra brand adaptation" notes for the category
- If **custom branding**: `ui-ux-pro-max` uses this guide to generate a category-appropriate palette

### During `/review`
Check that the design matches the category's mood and rationale. Flag mismatches:
- DeFi app with playful animations → "Consider reducing animation for financial credibility"
- Gaming app with minimal colors → "Consider adding more energy to match gaming UX expectations"

---

## References

- `knowledge/design-systems/integra-brand.md` — Official palette and tokens
- `knowledge/design-systems/custom-brand.md` — Custom palette template
- `knowledge/best-practices/ui-ux-design.md` — General UI/UX rules
- `knowledge/ui-skill-pipeline.md` — Skill pipeline execution order
