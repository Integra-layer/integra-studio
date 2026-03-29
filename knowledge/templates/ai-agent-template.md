# AI Agent dApp Template

## Standard Structure
```
my-agent-dapp/
├── contracts/
│   ├── AgentRegistry.sol         # Register custom agent types
│   ├── AgentStrategy.sol         # On-chain strategy parameters
│   └── interfaces/
│       ├── IAgentAuth.sol        # Integra's AgentAuth interface
│       └── IAgentStrategy.sol
├── agent-runtime/
│   ├── src/
│   │   ├── index.ts              # Agent entry point (Cloudflare Worker)
│   │   ├── strategy.ts           # Decision engine
│   │   ├── executor.ts           # Transaction execution via AgentAuth
│   │   ├── monitor.ts            # Market/state monitoring
│   │   └── config.ts             # Agent configuration
│   ├── templates/
│   │   ├── trading-agent.json
│   │   ├── portfolio-agent.json
│   │   └── custom-agent.json
│   └── wrangler.toml             # Cloudflare Worker config
├── frontend/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx              # Agent catalog
│   │   ├── deploy/page.tsx       # Deploy + configure agent
│   │   ├── dashboard/page.tsx    # Monitor agent activity
│   │   └── history/page.tsx      # Action history
│   ├── components/
│   │   ├── agent/
│   │   │   ├── AgentCard.tsx
│   │   │   ├── DeployModal.tsx
│   │   │   ├── ConfigForm.tsx
│   │   │   ├── ActivityFeed.tsx
│   │   │   └── KillSwitch.tsx
│   │   └── shared/
│   │       ├── ConnectButton.tsx
│   │       └── XPNotification.tsx
│   ├── hooks/
│   │   ├── useAgentAuth.ts
│   │   ├── useAgentStatus.ts
│   │   └── useAgentHistory.ts
│   └── lib/
│       └── contracts.ts
├── test/
│   ├── AgentRegistry.test.ts
│   └── strategy.test.ts
├── hardhat.config.ts
├── next.config.ts
└── docs/
```

## Agent Architecture on Integra

### AgentAuth Integration (REQUIRED)
Every agent dApp must integrate with Integra's `AgentAuth.sol`:
```solidity
interface IAgentAuth {
    function authorize(address agent, bytes4[] calldata allowedSelectors, uint256 spendLimit) external;
    function revoke(address agent) external;
    function executeAsUser(address target, bytes calldata data) external returns (bytes memory);
    function isAuthorized(address user, address agent, bytes4 selector) external view returns (bool);
}
```

### Agent Runtime Pattern
```typescript
// Agent runs as serverless function (Cloudflare Worker)
interface AgentConfig {
  name: string;
  owner: string;           // User's wallet address
  strategy: StrategyParams;
  permissions: {
    allowedContracts: string[];
    allowedSelectors: string[];
    spendLimit: bigint;
  };
  rateLimit: number;       // Max actions per minute (default: 10)
}

interface AgentAction {
  target: string;          // Contract address
  calldata: string;        // Encoded function call
  value: bigint;           // IRL to send
  reason: string;          // Human-readable explanation
}

// Agent loop
async function agentLoop(config: AgentConfig) {
  const state = await getMarketState();
  const decision = await strategy.evaluate(state, config.strategy);

  if (decision.shouldAct) {
    const action = decision.toAction();
    await agentAuth.executeAsUser(action.target, action.calldata);
    await logAction(action);
  }
}
```

### Strategy Types
| Type | Description | Complexity |
|------|-------------|-----------|
| Trading | Monitor GOB, auto-bid on undervalued assets | Medium |
| Portfolio | Rebalance across asset types | Medium |
| Minting | Batch-mint passports + wrap | Simple |
| Analyst | Monitor prices, alert user (read-only) | Simple |
| Arbitrage | Cross-dApp price differences | Advanced |
| Yield | Auto-compound, strategy switching | Advanced |

## XP Event Map
| Action | Points |
|--------|--------|
| Agent deployed | 200 |
| Agent action (manual XP rate) | 25 |
| Agent action (agent XP rate, 50%) | 12 |
| Agent stopped by user | 10 |

## Key Design Decisions
- Agents run SERVERLESS (Cloudflare Workers), not containers
- Rate limit: 10 actions/minute/agent (configurable)
- One agent = one user (no shared agents)
- IRL budget: unlimited on testnet
- Agent generates keypair on deploy, user authorizes via AgentAuth
