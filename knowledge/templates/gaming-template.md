# Gaming dApp Template

## Standard Structure
```
my-game-dapp/
├── contracts/
│   ├── GameState.sol             # On-chain game state
│   ├── GameItems.sol             # ERC-1155 game items
│   ├── Leaderboard.sol           # On-chain rankings
│   └── interfaces/
├── frontend/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx              # Game lobby
│   │   ├── play/page.tsx         # Main game view
│   │   ├── inventory/page.tsx    # Player items
│   │   └── leaderboard/page.tsx
│   ├── components/
│   │   ├── game/
│   │   │   ├── GameCanvas.tsx    # Phaser or HTML5 Canvas
│   │   │   ├── PlayerHUD.tsx
│   │   │   ├── ItemCard.tsx
│   │   │   ├── MatchCard.tsx
│   │   │   └── RewardModal.tsx
│   │   └── shared/
│   │       ├── ConnectButton.tsx
│   │       └── XPNotification.tsx
│   ├── hooks/
│   │   ├── useGameState.ts
│   │   ├── useInventory.ts
│   │   └── useLeaderboard.ts
│   └── lib/
│       └── contracts.ts
├── test/
└── docs/
```

## Gaming Patterns on Integra

### On-Chain Game Types
- **Prediction games** — bet on outcomes using IRL tokens
- **PvP battles** — stake assets, winner takes all
- **Collection games** — mint, trade, combine game items
- **Strategy** — deploy agents as game characters
- **Idle/incremental** — stake and earn over time

### Asset Passport as Game Items
Game items can be tokenized via Asset Passport:
- Items have real value through IRWA wrapping
- Tradeable on GOB
- Portable across games in the ecosystem

### Agent-Powered NPCs
Use AgentAuth to create AI-powered game characters:
- Trading NPCs on GOB
- Market makers in prediction games
- AI opponents in strategy games

## Token Integration

> Read `knowledge/networks/tokens.md` for full token registry and addresses.

- **IRL** (native) — entry fees, staking, rewards, gas
- **tUSDI** (`0xa640d8b5c9cb3b989881b8e63b0f30179c78a04f`) — prize pools, stable-value rewards, tournament prizes
- **WIRL** (`0x5002000000000000000000000000000000000001`) — wrapped IRL for in-game contract interactions

### Gaming Token Patterns
- Entry fees in IRL, prize pools in tUSDI (stable value)
- In-game currency backed by tUSDI for predictable item pricing
- Reward distributions in IRL for ecosystem engagement
- Show both balances in Player HUD

## XP Event Map
| Action | Points |
|--------|--------|
| Join game | 100 |
| Win match | 200 |
| Mint game item | 75 |
| Trade item | 50 |
| Reach leaderboard top 10 | 300 |
| Daily play streak | 25/day |
