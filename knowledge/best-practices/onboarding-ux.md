# Onboarding & User Experience Best Practices

Guidelines for onboarding users — especially those new to blockchain — into Integra dApps.

---

## 1. First Impression (0-30 seconds)

- **Hero section**: Clear value proposition in one sentence. No jargon.
- **Social proof**: Show number of users, transactions, or TVL if available
- **Single CTA**: "Get Started" or "Connect Wallet" — one clear action
- **Hide complexity**: Don't show wallet addresses, chain IDs, or gas concepts on the landing page
- **Fast load**: Above-fold content must render in <2.5s (LCP target)

---

## 2. Wallet Connection

### Web3Auth Flow
- **Social login first**: Show Google, X, Email buttons prominently
- **"Advanced" wallet option**: MetaMask/WalletConnect in a collapsible section
- **Success state**: Show avatar + truncated address + "Connected" badge
- **Error recovery**: Clear message if login fails, with retry button

### For Non-Crypto Users
- Never say "connect wallet" — say "Sign in" or "Get started"
- Don't explain what a wallet is unless the user asks
- After social login, show a brief tooltip: "Your wallet was created automatically. You're ready to go!"
- Offer a "Learn more" link for curious users, but don't force it

---

## 3. Progressive Disclosure

### Level 1: Core Actions (everyone sees)
- Main feature (swap, mint, deposit, play)
- Balance display (IRL + tUSDI)
- Transaction history

### Level 2: Advanced (show on interaction)
- Gas settings
- Slippage tolerance
- Advanced token management
- Network details

### Level 3: Expert (hidden behind settings)
- Raw contract interaction
- Custom RPC
- Developer tools
- Debug info

---

## 4. Testnet Onboarding

For testnet dApps, make getting test tokens frictionless:

1. Detect zero balance on first login
2. Show: "You need test tokens to get started. Claim free IRL + tUSDI from the faucet."
3. Provide direct link to `https://testnet.integralayer.com`
4. After claiming: auto-detect balance update and dismiss the prompt
5. Optional: embed faucet claim directly in the dApp if API allows

---

## 5. Guided Tutorials

### When to Show
- First-time users only (track with localStorage or backend)
- Dismissible with "Don't show again" option
- Re-accessible from Help/Settings menu

### Format
- **Tooltips**: Point to UI elements with brief explanations (max 2 sentences)
- **Step-by-step**: Numbered steps with progress indicator
- **Interactive**: Highlight the element user should interact with next
- Never block the entire screen — users should be able to dismiss and explore

---

## 6. Error Recovery

### Common Web3 Errors → User-Friendly Messages

| Technical Error | User Message | Action |
|----------------|-------------|--------|
| `insufficient funds` | "You don't have enough IRL for this transaction" | "Get test tokens from the faucet" button |
| `user rejected` | "Transaction cancelled" | "Try again" button |
| `nonce too low` | "A previous transaction is still processing" | "Wait for it to complete, then retry" |
| `gas estimation failed` | "This transaction would fail" | "Check your inputs and try again" |
| `network error` | "Can't connect to the network" | "Check your internet and retry" button |
| `wrong network` | "Please switch to Integra {Testnet/Mainnet}" | Auto-switch button |

---

## 7. Content Principles

- **Lead with outcomes**, not technology: "Earn rewards" not "Stake tokens to earn yield via our smart contract"
- **Explain on hover/click**, not upfront: Tooltips on technical terms
- **Use familiar analogies**: "Like a savings account" not "Liquidity pool with variable APY"
- **Show, don't tell**: Animations showing how features work > paragraphs of text
- **Localize numbers**: Use locale-appropriate formatting (commas, decimal points)

---

## 8. Engagement Patterns

### XP and Gamification
- Show XP earned after every action (toast: "+100 XP!")
- Progress bar toward next level/milestone
- Achievement badges for first actions (first mint, first trade, etc.)
- Leaderboard accessible but not intrusive

### Retention
- Daily check-in rewards (if applicable)
- Notification for pending actions (unclaimed rewards, expiring listings)
- "What's new" section for feature updates
- Share achievements to social media

---

## References

- `knowledge/best-practices/ui-ux-design.md` — General UX patterns
- `knowledge/best-practices/design-adaptation.md` — Category-specific UX
- `knowledge/networks/tokens.md` — Token info for balance displays
- `knowledge/integra-ecosystem.md` — XP system details
