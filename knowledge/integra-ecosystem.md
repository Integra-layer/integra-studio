# Integra Ecosystem Reference

Complete reference for all Integra blockchain products, contract interfaces, APIs, and integration patterns.

## Network Configuration

> Network config (chain IDs, RPCs, explorers, gas) is in `knowledge/networks/`. See `mainnet.md`, `testnet.md`, and `shared.md`. Do not duplicate network data here.

**Important:** The token is **IRL** / **airl** (NOT ILR/ailr). This is a common gotcha.

---

## Asset Passport

Tokenize and track real-world assets as NFTs (ERC-721 extension). Each passport is a unique digital twin of a physical asset with on-chain metadata, provenance history, and transfer tracking.

### Contract Interface

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

interface IAssetPassport {
    // --- Structs ---
    struct PassportData {
        string name;
        string category;        // e.g., "real-estate", "luxury-goods", "commodities"
        string metadataURI;     // IPFS or HTTP URI to full metadata JSON
        address issuer;         // Who created this passport
        uint256 createdAt;
        uint256 updatedAt;
        bool active;            // Can be deactivated by issuer
    }

    // --- Events ---
    event PassportMinted(uint256 indexed tokenId, address indexed issuer, string category);
    event MetadataUpdated(uint256 indexed tokenId, string newMetadataURI);
    event PassportDeactivated(uint256 indexed tokenId);
    event PassportReactivated(uint256 indexed tokenId);

    // --- Core Functions ---

    /// @notice Mint a new asset passport
    /// @param to Recipient address
    /// @param name Human-readable asset name
    /// @param category Asset category for classification
    /// @param metadataURI URI pointing to full metadata JSON
    /// @return tokenId The ID of the newly minted passport
    function mintPassport(
        address to,
        string calldata name,
        string calldata category,
        string calldata metadataURI
    ) external returns (uint256 tokenId);

    /// @notice Update the metadata URI of a passport
    /// @dev Only callable by the passport issuer
    function updateMetadata(uint256 tokenId, string calldata newMetadataURI) external;

    /// @notice Get full passport data
    function getPassportData(uint256 tokenId) external view returns (PassportData memory);

    /// @notice Get all passports owned by an address
    function getPassportsByOwner(address owner) external view returns (uint256[] memory);

    /// @notice Get all passports in a category
    function getPassportsByCategory(string calldata category) external view returns (uint256[] memory);

    /// @notice Deactivate a passport (issuer only)
    function deactivatePassport(uint256 tokenId) external;

    /// @notice Reactivate a passport (issuer only)
    function reactivatePassport(uint256 tokenId) external;

    /// @notice Check if a passport is active
    function isActive(uint256 tokenId) external view returns (bool);

    /// @notice Get the issuer of a passport
    function issuerOf(uint256 tokenId) external view returns (address);
}
```

### Integration Pattern

```typescript
// src/hooks/useAssetPassport.ts
import { useReadContract, useWriteContract, useWaitForTransactionReceipt } from "wagmi";
import { ASSET_PASSPORT_ABI } from "@/lib/abis/AssetPassport";
import { CONTRACTS } from "@/lib/contracts";

export function useMintPassport() {
  const { writeContract, data: hash, isPending } = useWriteContract();
  const { isLoading: isConfirming, isSuccess } = useWaitForTransactionReceipt({ hash });

  const mint = (to: `0x${string}`, name: string, category: string, metadataURI: string) => {
    writeContract({
      address: CONTRACTS.AssetPassport,
      abi: ASSET_PASSPORT_ABI,
      functionName: "mintPassport",
      args: [to, name, category, metadataURI],
    });
  };

  return { mint, isPending, isConfirming, isSuccess, hash };
}

export function usePassportData(tokenId: bigint) {
  return useReadContract({
    address: CONTRACTS.AssetPassport,
    abi: ASSET_PASSPORT_ABI,
    functionName: "getPassportData",
    args: [tokenId],
  });
}
```

---

## IRWAToken (Wrapper)

Wrap passported assets into fungible ERC-20 tokens. Enables fractional ownership, liquidity, and DeFi composability for real-world assets.

### Contract Interface

```solidity
interface IIRWAToken {
    // --- Events ---
    event Wrapped(uint256 indexed passportId, address indexed wrapper, uint256 amount);
    event Unwrapped(uint256 indexed passportId, address indexed unwrapper, uint256 amount);
    event WrapRatioUpdated(uint256 indexed passportId, uint256 newRatio);

    // --- Core Functions ---

    /// @notice Wrap a passport into fungible tokens
    /// @param passportId The Asset Passport token ID to wrap
    /// @param amount Number of tokens to mint (based on wrap ratio)
    /// @dev Caller must own the passport or have approval
    function wrap(uint256 passportId, uint256 amount) external;

    /// @notice Unwrap tokens back to reclaim the passport
    /// @param passportId The passport to unwrap
    /// @param amount Number of tokens to burn
    function unwrap(uint256 passportId, uint256 amount) external;

    /// @notice Get total wrapped supply for a specific passport
    function totalWrapped(uint256 passportId) external view returns (uint256);

    /// @notice Get the underlying passport ID for this token pool
    function underlyingPassport(uint256 poolId) external view returns (uint256 passportId);

    /// @notice Get the wrap ratio (tokens per passport unit)
    function wrapRatio(uint256 passportId) external view returns (uint256);

    /// @notice Check how many tokens an address holds for a specific passport pool
    function balanceOfPool(address account, uint256 passportId) external view returns (uint256);

    /// @notice Get all passport IDs that have been wrapped
    function getWrappedPassports() external view returns (uint256[] memory);
}
```

### Integration Pattern

```typescript
export function useWrapAsset() {
  const { writeContract, data: hash, isPending } = useWriteContract();
  const { isLoading: isConfirming, isSuccess } = useWaitForTransactionReceipt({ hash });

  const wrap = (passportId: bigint, amount: bigint) => {
    writeContract({
      address: CONTRACTS.IRWAToken,
      abi: IRWA_TOKEN_ABI,
      functionName: "wrap",
      args: [passportId, amount],
    });
  };

  return { wrap, isPending, isConfirming, isSuccess, hash };
}
```

---

## Global Order Book (GOB)

Decentralized OTC marketplace for trading any asset pair. Supports limit orders, negotiation, and partial fills.

### Contract Interface

```solidity
interface IGlobalOrderBook {
    // --- Structs ---
    struct Order {
        uint256 orderId;
        address maker;
        address sellToken;
        address buyToken;
        uint256 sellAmount;
        uint256 buyAmount;
        uint256 filledAmount;
        uint256 createdAt;
        uint256 expiresAt;
        OrderStatus status;
    }

    enum OrderStatus { Open, PartiallyFilled, Filled, Cancelled, Expired }

    // --- Events ---
    event OrderCreated(uint256 indexed orderId, address indexed maker, address sellToken, address buyToken);
    event OrderFilled(uint256 indexed orderId, address indexed taker, uint256 fillAmount);
    event OrderCancelled(uint256 indexed orderId);
    event OrderExpired(uint256 indexed orderId);

    // --- Core Functions ---

    /// @notice Create a new limit order
    /// @param sellToken Token to sell
    /// @param buyToken Token to buy
    /// @param sellAmount Amount of sellToken to sell
    /// @param buyAmount Amount of buyToken to receive
    /// @param expiresAt Timestamp when order expires (0 = no expiry)
    function createOrder(
        address sellToken,
        address buyToken,
        uint256 sellAmount,
        uint256 buyAmount,
        uint256 expiresAt
    ) external returns (uint256 orderId);

    /// @notice Fill an existing order (fully or partially)
    /// @param orderId The order to fill
    /// @param amount Amount of buyToken to provide
    function fillOrder(uint256 orderId, uint256 amount) external;

    /// @notice Cancel an open order (maker only)
    function cancelOrder(uint256 orderId) external;

    /// @notice Get order details
    function getOrder(uint256 orderId) external view returns (Order memory);

    /// @notice Get the order book for a token pair
    /// @param sellToken The token being sold
    /// @param buyToken The token being bought
    /// @param offset Pagination offset
    /// @param limit Max results
    function getOrderBook(
        address sellToken,
        address buyToken,
        uint256 offset,
        uint256 limit
    ) external view returns (Order[] memory);

    /// @notice Get all orders for a maker
    function getOrdersByMaker(address maker) external view returns (uint256[] memory);
}
```

### Integration Pattern

```typescript
export function useCreateOrder() {
  const { writeContract, data: hash, isPending } = useWriteContract();
  const { isLoading: isConfirming, isSuccess } = useWaitForTransactionReceipt({ hash });

  const createOrder = (
    sellToken: `0x${string}`,
    buyToken: `0x${string}`,
    sellAmount: bigint,
    buyAmount: bigint,
    expiresAt: bigint
  ) => {
    writeContract({
      address: CONTRACTS.GlobalOrderBook,
      abi: GOB_ABI,
      functionName: "createOrder",
      args: [sellToken, buyToken, sellAmount, buyAmount, expiresAt],
    });
  };

  return { createOrder, isPending, isConfirming, isSuccess, hash };
}

export function useOrderBook(sellToken: `0x${string}`, buyToken: `0x${string}`) {
  return useReadContract({
    address: CONTRACTS.GlobalOrderBook,
    abi: GOB_ABI,
    functionName: "getOrderBook",
    args: [sellToken, buyToken, 0n, 50n],
  });
}
```

---

## Agent Arena (AgentAuth)

Platform for building, deploying, and monetizing AI agents with on-chain identity and authentication.

### Contract Interface

```solidity
interface IAgentAuth {
    // --- Structs ---
    struct AgentProfile {
        uint256 agentId;
        address owner;
        string name;
        string description;
        string endpointURI;       // Where the agent is hosted
        string[] capabilities;    // List of capability tags
        uint256 reputation;       // Accumulated reputation score
        uint256 tasksCompleted;
        uint256 registeredAt;
        bool active;
    }

    struct TaskResult {
        uint256 taskId;
        uint256 agentId;
        bool success;
        string resultURI;         // IPFS or HTTP URI to result data
        uint256 executedAt;
    }

    // --- Events ---
    event AgentRegistered(uint256 indexed agentId, address indexed owner, string name);
    event AgentUpdated(uint256 indexed agentId);
    event AgentDeactivated(uint256 indexed agentId);
    event TaskExecuted(uint256 indexed taskId, uint256 indexed agentId, bool success);
    event RewardDistributed(uint256 indexed agentId, uint256 amount);

    // --- Core Functions ---

    /// @notice Register a new AI agent
    function registerAgent(
        string calldata name,
        string calldata description,
        string calldata endpointURI,
        string[] calldata capabilities
    ) external returns (uint256 agentId);

    /// @notice Update agent profile
    function updateAgent(
        uint256 agentId,
        string calldata description,
        string calldata endpointURI,
        string[] calldata capabilities
    ) external;

    /// @notice Execute a task via an agent
    /// @param agentId The agent to execute the task
    /// @param taskData Encoded task parameters
    function executeTask(uint256 agentId, bytes calldata taskData) external returns (uint256 taskId);

    /// @notice Submit task result (called by agent owner)
    function submitResult(uint256 taskId, bool success, string calldata resultURI) external;

    /// @notice Get agent profile
    function getAgentProfile(uint256 agentId) external view returns (AgentProfile memory);

    /// @notice Get all agents by owner
    function getAgentsByOwner(address owner) external view returns (uint256[] memory);

    /// @notice Get agents by capability
    function getAgentsByCapability(string calldata capability) external view returns (uint256[] memory);

    /// @notice Get task result
    function getTaskResult(uint256 taskId) external view returns (TaskResult memory);

    /// @notice Deactivate an agent (owner only)
    function deactivateAgent(uint256 agentId) external;
}
```

### Integration Pattern

```typescript
export function useRegisterAgent() {
  const { writeContract, data: hash, isPending } = useWriteContract();
  const { isLoading: isConfirming, isSuccess } = useWaitForTransactionReceipt({ hash });

  const register = (
    name: string,
    description: string,
    endpointURI: string,
    capabilities: string[]
  ) => {
    writeContract({
      address: CONTRACTS.AgentAuth,
      abi: AGENT_AUTH_ABI,
      functionName: "registerAgent",
      args: [name, description, endpointURI, capabilities],
    });
  };

  return { register, isPending, isConfirming, isSuccess, hash };
}
```

---

## XP System

Cross-dApp gamification layer that rewards user participation. Any Integra dApp can emit XP events, and users accumulate XP across the ecosystem.

### Contract Interface

```solidity
interface IXPSystem {
    // --- Structs ---
    struct XPEvent {
        address dApp;           // The dApp that emitted the XP
        address user;
        uint256 amount;
        string category;        // "interact", "trade", "create", "governance", "social"
        string action;          // Specific action description
        uint256 timestamp;
    }

    // --- Events ---
    event XPEarned(address indexed user, address indexed dApp, uint256 amount, string category);

    // --- Core Functions ---

    /// @notice Emit XP for a user action (callable by registered dApps only)
    /// @param user The user earning XP
    /// @param amount Amount of XP to award
    /// @param category XP category
    /// @param action Description of the action
    function emitXP(
        address user,
        uint256 amount,
        string calldata category,
        string calldata action
    ) external;

    /// @notice Get total XP for a user
    function getUserXP(address user) external view returns (uint256);

    /// @notice Get XP breakdown by category for a user
    function getUserXPByCategory(address user, string calldata category) external view returns (uint256);

    /// @notice Get the global leaderboard
    /// @param offset Pagination offset
    /// @param limit Max results
    function getLeaderboard(uint256 offset, uint256 limit) external view returns (
        address[] memory users,
        uint256[] memory scores
    );

    /// @notice Get a user's rank
    function getUserRank(address user) external view returns (uint256);

    /// @notice Register a dApp to emit XP (governance controlled)
    function registerDApp(address dApp) external;

    /// @notice Check if a dApp is registered
    function isDAppRegistered(address dApp) external view returns (bool);
}
```

### API Endpoints

```
Base URL: https://api.integralayer.com/xp

GET  /api/xp/{address}                    — Get user's total XP and breakdown
GET  /api/xp/{address}/history             — Get user's XP event history
GET  /api/leaderboard?offset=0&limit=100   — Get global leaderboard
GET  /api/leaderboard/category/{category}  — Get category-specific leaderboard
POST /api/xp/emit                          — Emit XP (authenticated, for backend integrations)
```

### XP Categories

| Category | Description | Example Actions |
|----------|-------------|-----------------|
| `interact` | Basic platform interaction | Connect wallet, visit page, complete tutorial |
| `trade` | Trading and financial actions | Create order, fill order, wrap/unwrap |
| `create` | Content and asset creation | Mint passport, register agent, create proposal |
| `governance` | Governance participation | Vote on proposal, delegate, discuss |
| `social` | Social and community actions | Refer friend, share, comment, review |

### XP Amounts (Recommended)

| Action | Category | XP Amount |
|--------|----------|-----------|
| First wallet connection | interact | 50 |
| Complete onboarding | interact | 100 |
| Mint asset passport | create | 200 |
| Wrap asset | trade | 150 |
| Create order on GOB | trade | 100 |
| Fill order on GOB | trade | 100 |
| Register AI agent | create | 300 |
| Vote on governance | governance | 150 |
| Refer a friend | social | 250 |

### Integration Pattern

```typescript
// src/hooks/useXP.ts
export function useEmitXP() {
  const { writeContract, data: hash, isPending } = useWriteContract();

  const emitXP = (user: `0x${string}`, amount: bigint, category: string, action: string) => {
    writeContract({
      address: CONTRACTS.XPSystem,
      abi: XP_SYSTEM_ABI,
      functionName: "emitXP",
      args: [user, amount, category, action],
    });
  };

  return { emitXP, isPending, hash };
}

export function useUserXP(address: `0x${string}`) {
  return useReadContract({
    address: CONTRACTS.XPSystem,
    abi: XP_SYSTEM_ABI,
    functionName: "getUserXP",
    args: [address],
  });
}

// Helper to emit XP after a successful transaction
export function useXPOnSuccess(category: string, action: string, amount: bigint) {
  const { emitXP } = useEmitXP();
  const { address } = useAccount();

  const emitAfterSuccess = () => {
    if (address) {
      emitXP(address, amount, category, action);
    }
  };

  return { emitAfterSuccess };
}
```

---

## IntegraLending

Lending and borrowing protocol for Integra assets. Users deposit assets to earn yield, and borrowers can take loans against collateral.

### Contract Interface

```solidity
interface IIntegraLending {
    // --- Structs ---
    struct MarketInfo {
        address token;
        uint256 totalDeposited;
        uint256 totalBorrowed;
        uint256 depositAPY;        // Basis points (e.g., 500 = 5%)
        uint256 borrowAPY;
        uint256 collateralFactor;  // Basis points (e.g., 7500 = 75%)
        uint256 liquidationThreshold;
        bool active;
    }

    struct UserPosition {
        uint256 deposited;
        uint256 borrowed;
        uint256 healthFactor;      // 1e18 = 1.0, < 1.0 = liquidatable
    }

    // --- Events ---
    event Deposited(address indexed user, address indexed token, uint256 amount);
    event Withdrawn(address indexed user, address indexed token, uint256 amount);
    event Borrowed(address indexed user, address indexed token, uint256 amount);
    event Repaid(address indexed user, address indexed token, uint256 amount);
    event Liquidated(address indexed user, address indexed liquidator, address token, uint256 amount);

    // --- Core Functions ---

    /// @notice Deposit assets to earn yield
    function deposit(address token, uint256 amount) external;

    /// @notice Withdraw deposited assets
    function withdraw(address token, uint256 amount) external;

    /// @notice Borrow assets against deposited collateral
    function borrow(address token, uint256 amount) external;

    /// @notice Repay borrowed assets
    function repay(address token, uint256 amount) external;

    /// @notice Liquidate an undercollateralized position
    function liquidate(address user, address token, uint256 amount) external;

    /// @notice Get the health factor of a user's position
    function getHealthFactor(address user) external view returns (uint256);

    /// @notice Get user's position for a specific token
    function getUserPosition(address user, address token) external view returns (UserPosition memory);

    /// @notice Get market info for a specific token
    function getMarketInfo(address token) external view returns (MarketInfo memory);

    /// @notice Get all active markets
    function getActiveMarkets() external view returns (address[] memory);
}
```

---

## PriceOracle

On-chain price feeds for Integra assets. Provides reliable pricing for DeFi operations, liquidations, and portfolio valuation.

### Contract Interface

```solidity
interface IPriceOracle {
    // --- Structs ---
    struct PriceData {
        uint256 price;          // Price in USD with 8 decimals (e.g., 100_000_000 = $1.00)
        uint256 timestamp;
        uint256 roundId;
    }

    // --- Events ---
    event PriceUpdated(address indexed token, uint256 price, uint256 roundId);

    // --- Core Functions ---

    /// @notice Get the current price for a token
    /// @param token The token address
    /// @return price Price in USD with 8 decimals
    function getPrice(address token) external view returns (uint256 price);

    /// @notice Get full price data including timestamp
    function getLatestRound(address token) external view returns (PriceData memory);

    /// @notice Get historical price by round ID
    function getRound(address token, uint256 roundId) external view returns (PriceData memory);

    /// @notice Update price (authorized reporters only)
    function updatePrice(address token, uint256 price) external;

    /// @notice Check if a price feed exists for a token
    function hasFeed(address token) external view returns (bool);

    /// @notice Get all tokens with price feeds
    function getSupportedTokens() external view returns (address[] memory);
}
```

---

## Faucet

Testnet token dispenser for development and testing.

### API

```
Base URL: https://faucet.integralayer.com

POST /api/faucet/drip
Body: { "address": "0x..." }
Response: { "txHash": "0x...", "amount": "1000000000000000000", "cooldown": 86400 }

GET /api/faucet/status/{address}
Response: { "lastDrip": 1711756800, "canDripAt": 1711843200, "canDrip": false }
```

### Limits

- Amount per drip: 1 IRL (1e18 airl)
- Cooldown: 24 hours per address
- Rate limit: 10 requests per minute per IP

### Integration Pattern

```typescript
// src/lib/faucet.ts
const FAUCET_URL = "https://faucet.integralayer.com";

export async function requestTestnetIRL(address: string): Promise<{ txHash: string }> {
  const res = await fetch(`${FAUCET_URL}/api/faucet/drip`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ address }),
  });

  if (!res.ok) {
    const error = await res.json();
    throw new Error(error.message || "Faucet request failed");
  }

  return res.json();
}

export async function checkFaucetStatus(address: string): Promise<{ canDrip: boolean; canDripAt: number }> {
  const res = await fetch(`${FAUCET_URL}/api/faucet/status/${address}`);
  return res.json();
}
```

---

## Known Contract Addresses

> **Note:** These are placeholder addresses for testnet. Update with actual deployed addresses after deployment.

```typescript
// src/lib/contracts.ts
export const CONTRACTS = {
  AssetPassport: "0x..." as `0x${string}`,
  IRWAToken: "0x..." as `0x${string}`,
  GlobalOrderBook: "0x..." as `0x${string}`,
  AgentAuth: "0x..." as `0x${string}`,
  XPSystem: "0x..." as `0x${string}`,
  IntegraLending: "0x..." as `0x${string}`,
  PriceOracle: "0x..." as `0x${string}`,
} as const;
```

When actual testnet contracts are deployed by the Integra team, update these addresses. Check the Integra documentation or explorer for the latest deployed addresses.

---

## ERC-7943 Compliance Standard

Universal RWA compliance interface, currently at Last Call status.

### Primitives

| Function | Purpose |
|----------|---------|
| `isTransferAllowed()` | Check if a transfer is permitted under compliance rules |
| `isUserAllowed()` | Verify user eligibility (KYC/AML/jurisdiction) |
| `forceTransfer()` | Regulator-initiated forced transfer (freeze recovery, court order) |
| `setFrozen()` | Freeze/unfreeze an account or token |

- Co-designed with MetaMask, Google, Coinbase
- Integra is an ERC-3643 T-REX association member
- IRWA wrapper preserves all compliance rules from the source token — wrapping does not bypass compliance

---

## Agent Passport Protocol (ERC-8004 + ERC-6551)

On-chain identity and trust layer for AI agents.

- Every AI agent gets on-chain identity via ERC-8004 registry
- Token-bound account (ERC-6551) gives each agent its own wallet
- Agents stake IRL tokens for trust/reputation
- Slashing mechanism for poor performance (failed tasks, malicious behavior)
- Non-transferable reputation tokens — reputation cannot be bought or sold

---

## Gasless Operations

Built into Cosmos SDK, no relay server needed.

| Module | Purpose |
|--------|---------|
| `x/feegrant` | Grant gas allowances to other accounts (sponsor user tx fees) |
| `x/authz` | Authorize agents to execute transactions on behalf of the owner |

- Combined with AgentAuth for full agent delegation: owner authorizes agent via authz, agent pays no gas via feegrant
- No external relay infrastructure required — native chain-level support

---

## Cross-Chain (IBC)

- **Interchain Accounts:** Agent staked on Integra can execute on other Cosmos chains
- **IBC Transfers:** Token portability across IBC-connected chains
- **Single Registration, Multi-Chain Operations:** Register once on Integra, operate everywhere

---

## IRWA Token Bridge Architecture

Lock-and-mint bridge for cross-chain RWA portability.

| Component | Role |
|-----------|------|
| Lock Vault | Non-upgradeable escrow for original tokens |
| Wrapper Factory | Deploys per-token IRWA contracts on destination chain |
| Oracle MVP | Polls original contract, syncs compliance state cross-chain |
| Bridge | Lock-and-mint mechanism for cross-chain portability |

**Fail-closed design:** If oracle is stale for > N blocks, all transfers are blocked until oracle catches up. Safety over liveness.

---

## Known Addresses & References

| Resource | Location |
|----------|----------|
| Knowledge bank | `/Users/adamboudj/knowledge-bank/01-integra/` |
| Strategy repo | `/Users/adamboudj/projects/integra-ai-strategy/` |
| Research campaigns | `integra-ai-strategy/research/` (13 campaigns) |
| Official docs export | `/Users/adamboudj/knowledge-bank/01-integra/docs-export/` |
| Whitepaper | `/Users/adamboudj/knowledge-bank/01-integra/company/integralayer-whitepaper.md` |

---

## Web3Auth Configuration

```typescript
// src/lib/web3auth.ts
import { Web3Auth } from "@web3auth/modal";
import { CHAIN_NAMESPACES, WEB3AUTH_NETWORK } from "@web3auth/base";
import { EthereumPrivateKeyProvider } from "@web3auth/ethereum-provider";

const chainConfig = {
  chainNamespace: CHAIN_NAMESPACES.EIP155,
  chainId: "0x6669", // 26217 in hex
  rpcTarget: "<RPC URL from knowledge/networks/ for your target network>",
  displayName: "Integra Testnet",
  blockExplorerUrl: "https://testnet-explorer.integralayer.com",
  ticker: "IRL",
  tickerName: "IRL",
};

const privateKeyProvider = new EthereumPrivateKeyProvider({
  config: { chainConfig },
});

export const web3auth = new Web3Auth({
  clientId: process.env.NEXT_PUBLIC_WEB3AUTH_CLIENT_ID || "BM4-vTeJRs0OW-iD2zqCUdNEbgqW-dEGMWUS53FVYpUjnKZqaBP_0njivHaDPZnNzJ8jfDd6b8gY_p0ROmIs6Jc",
  web3AuthNetwork: WEB3AUTH_NETWORK.SAPPHIRE_DEVNET, // Use SAPPHIRE_MAINNET for production
  privateKeyProvider,
  uiConfig: {
    appName: "Integra",
    mode: "dark",
    loginMethodsOrder: ["google", "twitter", "discord", "github"],
  },
});

// JWKS endpoint for backend JWT verification:
// https://api-auth.web3auth.io/.well-known/jwks.json
```
