# Social dApp Template

## Standard Structure
```
my-social-dapp/
├── contracts/
│   ├── Profile.sol               # On-chain user profiles (ERC-721)
│   ├── Reputation.sol            # XP-based reputation scores
│   ├── SocialActions.sol         # Follow, endorse, rate
│   └── interfaces/
├── frontend/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx              # Feed / discovery
│   │   ├── profile/[address]/page.tsx
│   │   ├── create-profile/page.tsx
│   │   └── leaderboard/page.tsx
│   ├── components/
│   │   ├── social/
│   │   │   ├── ProfileCard.tsx
│   │   │   ├── ActivityFeed.tsx
│   │   │   ├── FollowButton.tsx
│   │   │   ├── ReputationBadge.tsx
│   │   │   └── EndorseModal.tsx
│   │   └── shared/
│   │       ├── ConnectButton.tsx
│   │       └── XPNotification.tsx
│   ├── hooks/
│   │   ├── useProfile.ts
│   │   ├── useReputation.ts
│   │   └── useSocialActions.ts
│   └── lib/
│       └── contracts.ts
├── test/
└── docs/
```

## Social Patterns on Integra

### Profile System
- Profiles as ERC-721 tokens (one per address, soulbound optional)
- Linked to XP score from the ecosystem
- Display Asset Passports owned, agents deployed, trades completed
- Reputation score computed from XP + endorsements

### Reputation Mechanics
- Endorsements from other users (costs XP to endorse, prevents spam)
- Activity-based: more ecosystem engagement = higher reputation
- Badge system: "Early Adopter", "Top Trader", "Agent Master", "Builder"
- Reputation gates: certain features require minimum reputation

### Feed Types
- Global activity feed (all ecosystem events)
- Following feed (users you follow)
- Category feeds (DeFi, AI, Trading)
- Agent activity feeds

## Token Integration

> Read `knowledge/networks/tokens.md` for full token registry and addresses.

- **IRL** (native) — governance voting, staking for reputation, gas
- **tUSDI** (`0xa640d8b5c9cb3b989881b8e63b0f30179c78a04f`) — tipping, micropayments, bounties
- **WIRL** (`0x5002000000000000000000000000000000000001`) — wrapped IRL for governance contracts

### Social Token Patterns
- Tipping other users in tUSDI (small, stable-value amounts)
- Staking IRL for reputation weight (more stake = more influence)
- Bounties posted in tUSDI for content creation or tasks
- Show user's IRL + tUSDI balance on profile page

## XP Event Map
| Action | Points |
|--------|--------|
| Create profile | 200 |
| Follow user | 25 |
| Endorse user | 50 |
| Receive endorsement | 75 |
| Post update | 50 |
| First interaction | 100 |
