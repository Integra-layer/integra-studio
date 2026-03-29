# DeFi dApp Template

## Standard Structure
```
my-defi-dapp/
├── contracts/
│   ├── core/
│   │   ├── Pool.sol              # Main liquidity/lending pool
│   │   ├── Vault.sol             # Asset vault with strategies
│   │   └── Router.sol            # Entry point for user actions
│   ├── interfaces/
│   │   ├── IPool.sol
│   │   ├── IVault.sol
│   │   └── IIntegraIntegration.sol
│   ├── libraries/
│   │   ├── MathLib.sol           # Fixed-point math
│   │   └── XPLib.sol             # XP event helpers
│   └── mocks/
│       └── MockOracle.sol
├── frontend/
│   ├── app/
│   │   ├── layout.tsx            # Root layout with Web3Auth provider
│   │   ├── page.tsx              # Dashboard
│   │   ├── deposit/page.tsx
│   │   ├── withdraw/page.tsx
│   │   └── positions/page.tsx
│   ├── components/
│   │   ├── layout/
│   │   │   ├── Header.tsx
│   │   │   ├── Sidebar.tsx
│   │   │   └── Footer.tsx
│   │   ├── defi/
│   │   │   ├── PoolCard.tsx
│   │   │   ├── DepositForm.tsx
│   │   │   ├── PositionRow.tsx
│   │   │   └── APYBadge.tsx
│   │   └── shared/
│   │       ├── ConnectButton.tsx  # Web3Auth
│   │       ├── XPNotification.tsx
│   │       └── TokenAmount.tsx
│   ├── hooks/
│   │   ├── usePool.ts
│   │   ├── usePosition.ts
│   │   └── useXP.ts
│   └── lib/
│       ├── contracts.ts          # ABI + address registry
│       └── integra.ts            # Integra ecosystem helpers
├── test/
│   ├── Pool.test.ts
│   ├── Vault.test.ts
│   └── integration.test.ts
├── ignition/
│   └── modules/
│       └── DefiDapp.ts
├── hardhat.config.ts
├── next.config.ts
├── package.json
├── .env.example
└── docs/
    ├── PRD.md
    ├── ARCHITECTURE.md
    ├── CONTRACTS.md
    └── FRONTEND.md
```

## Common DeFi Patterns on Integra

### Lending Pool
- Accept IRWA tokens as collateral (from Wrapper)
- Use PriceOracle for valuations
- Configurable LTV ratios per asset type
- Health factor monitoring
- Liquidation mechanics

### Staking
- Accept IRL token deposits
- Time-weighted rewards
- Delegation to validators
- Compound strategies

### Yield Vault
- Auto-compound strategies
- Multiple asset support
- Fee structures (management + performance)
- Withdrawal queues for illiquid assets

### DEX/Swap
- Constant product AMM (x*y=k)
- Or order book via GOB integration
- Slippage protection
- LP token minting

## Required Integra Integrations
- **Web3Auth**: Social login for all users
- **XP Events**: Emit on deposit, withdraw, claim, compound
- **Asset Passport**: Accept passport-backed IRWA tokens
- **AgentAuth**: Allow AI agents to manage positions
- **Subdomain**: xyz.integralayer.com hosting

## XP Event Map
| Action | Event | Points |
|--------|-------|--------|
| First deposit | `XPAction(user, "first_deposit", 200)` | 200 |
| Deposit | `XPAction(user, "deposit", 100)` | 100 |
| Withdraw | `XPAction(user, "withdraw", 50)` | 50 |
| Claim rewards | `XPAction(user, "claim", 75)` | 75 |
| Compound | `XPAction(user, "compound", 100)` | 100 |
| Liquidation participation | `XPAction(user, "liquidate", 150)` | 150 |

## Solidity Starter
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/utils/ReentrancyGuard.sol";

interface IXPSystem {
    event XPAction(address indexed user, string actionType, uint256 points);
}

contract IntegraPool is Ownable, ReentrancyGuard, IXPSystem {
    IERC20 public immutable depositToken;

    struct Position {
        uint256 deposited;
        uint256 depositTime;
        uint256 lastClaimTime;
    }

    mapping(address => Position) public positions;
    uint256 public totalDeposited;

    constructor(address _depositToken) Ownable(msg.sender) {
        depositToken = IERC20(_depositToken);
    }

    function deposit(uint256 amount) external nonReentrant {
        require(amount > 0, "Amount must be > 0");
        depositToken.transferFrom(msg.sender, address(this), amount);

        positions[msg.sender].deposited += amount;
        positions[msg.sender].depositTime = block.timestamp;
        totalDeposited += amount;

        emit XPAction(msg.sender, "deposit", 100);
    }

    function withdraw(uint256 amount) external nonReentrant {
        require(positions[msg.sender].deposited >= amount, "Insufficient balance");
        positions[msg.sender].deposited -= amount;
        totalDeposited -= amount;

        depositToken.transfer(msg.sender, amount);
        emit XPAction(msg.sender, "withdraw", 50);
    }
}
```
