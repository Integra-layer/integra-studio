# Infrastructure/Tool dApp Template

## Standard Structure
```
my-infra-tool/
├── contracts/
│   ├── Oracle.sol                # Data feed oracle
│   ├── Registry.sol              # Service registry
│   └── interfaces/
├── backend/
│   ├── src/
│   │   ├── index.ts              # API entry point
│   │   ├── indexer.ts            # Chain event indexer
│   │   ├── aggregator.ts         # Data aggregation
│   │   └── api/
│   │       ├── routes.ts
│   │       └── middleware.ts
│   └── Dockerfile
├── frontend/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx              # Dashboard
│   │   └── docs/page.tsx         # API documentation
│   ├── components/
│   │   ├── dashboard/
│   │   │   ├── MetricCard.tsx
│   │   │   ├── ChartPanel.tsx
│   │   │   ├── EventLog.tsx
│   │   │   └── StatusGrid.tsx
│   │   └── shared/
│   │       └── ConnectButton.tsx
│   └── hooks/
│       ├── useMetrics.ts
│       └── useEvents.ts
├── test/
└── docs/
```

## Infrastructure Types on Integra

### Analytics Dashboard
- Index all ecosystem contract events
- Aggregate: TVL, volume, users, transactions
- Real-time charts and metrics
- Per-dApp breakdown

### Oracle
- External data feeds (prices, weather, sports)
- Push or pull model
- Used by DeFi dApps for valuations

### Developer SDK
- npm package wrapping Integra contract interactions
- TypeScript-first, tree-shakeable
- Handles Web3Auth, contract calls, XP events
- Published to npm as @integra/sdk

### Bridge
- Connect Integra testnet to other testnets
- Lock-and-mint or burn-and-mint
- Support Asset Passport portability

## Token Integration

> Read `knowledge/networks/tokens.md` for full token registry and addresses.

- **IRL** (native) — gas costs, API access fees
- **tUSDI** (`0xa640d8b5c9cb3b989881b8e63b0f30179c78a04f`) — API credit purchases, data feed subscriptions
- **WIRL** (`0x5002000000000000000000000000000000000001`) — wrapped IRL for payment contracts

### Infrastructure Token Patterns
- Show gas costs prominently in dashboards
- tUSDI for API credit system (if applicable)
- Oracle feeds should include IRL and tUSDI price data
- SDKs should expose helpers for both token types

## XP Event Map
| Action | Points |
|--------|--------|
| Use infrastructure tool | 50 |
| Provide data feed | 100 |
| SDK integration | 150 |
| Report bug/issue | 75 |
