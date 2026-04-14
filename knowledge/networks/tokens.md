# Integra Token Registry

## Native IRL Token

| Property | Value |
|----------|-------|
| Name | Integra |
| Symbol | IRL |
| Smallest Unit | airl |
| EVM Decimals | 18 |
| Cosmos SDK Decimals | 6 |
| Purpose | Native gas token + value storage |

> **CRITICAL:** See [shared.md](./shared.md) for dual decimal warning when bridging between EVM and Cosmos layers.

## Standard Test Tokens (Testnet Only)

### tUSDI (Test USD Stablecoin)

| Property | Value |
|----------|-------|
| Name | Test USD Stablecoin |
| Symbol | tUSDI |
| Address (Testnet) | `0xa640d8b5c9cb3b989881b8e63b0f30179c78a04f` |
| Standard | ERC-20 |
| Decimals | 18 |
| Source | Testnet Faucet |
| Faucet Distribution | 1,000 tUSDI per request (24h cooldown) |
| Purpose | Testing DeFi apps, stablecoins, payment flows |
| Production Equivalent | Real USDI (mainnet) |

> **Note:** tUSDI is distributed alongside 10 IRL from the faucet at https://faucet.integralayer.com

## Testnet Faucet Distribution

When users claim from the faucet:
- **10 IRL** — native token for gas + general use
- **1,000 tUSDI** — stablecoin for testing ERC-20, DEX, lending, and payment features

## Using Tokens in dApps

### For DeFi Apps (Lending, DEX, Yield)
- Recommend using both **IRL** (for gas) and **tUSDI** (for stablecoin pair trading)
- Example pairs: IRL/tUSDI, tUSDI/other
- Contracts should accept ERC-20 transfers

### For Native Asset Apps (NFTs, RWA)
- Use **IRL** for payments and gas
- Optional: Accept **tUSDI** as secondary payment method

### For Multi-Token Apps
- Primary: **IRL** (gas + value)
- Secondary: **tUSDI** (liquidity, trading pairs)
- Tertiary: **WIRL** (wrapped IRL for contract interactions)

## WIRL (Wrapped IRL)

| Property | Value |
|----------|-------|
| Name | Wrapped IRL |
| Symbol | WIRL |
| Address | `0x5002000000000000000000000000000000000001` |
| Standard | ERC-20 |
| Purpose | Allow native IRL to be used in smart contracts |
| Decimals | 18 |

> WIRL is a precompile contract that wraps native IRL. Always use WIRL when your contract needs to interact with IRL as an ERC-20 token.

## Token Selection Guide for New dApps

| dApp Type | Primary Token | Secondary | Tertiary |
|-----------|---------------|-----------|----------|
| DEX / AMM | IRL + tUSDI pair | WIRL | — |
| Lending Pool | IRL | tUSDI | WIRL |
| NFT Marketplace | IRL | tUSDI (optional) | — |
| Real-World Assets | IRL | tUSDI (optional) | — |
| Governance DAO | IRL | — | — |
| Staking / Yield | IRL + WIRL | tUSDI | — |
| Payment App | tUSDI (primary) | IRL (gas) | — |

## Migration: Testnet → Mainnet

When moving from testnet to mainnet:

1. **Replace tUSDI** → Real USDI token address (to be determined by Integra)
2. **Keep IRL** → Native token remains the same (Chain ID 26217)
3. **WIRL address** → Remains `0x5002000000000000000000000000000000000001` (precompile)
4. **Faucet** → Not available on mainnet. Users need real IRL from exchanges/wallets.

## Common Integration Pattern (Solidity)

```solidity
// For testnet
address constant tUSDI = 0xa640d8b5c9cb3b989881b8e63b0f30179c78a04f;
address constant IRL = 0x5002000000000000000000000000000000000001; // WIRL

// For mainnet
// address constant USDI = 0x...; // (actual mainnet USDI address)
// address constant IRL = 0x5002000000000000000000000000000000000001; // WIRL stays same
```

## References

- [Shared Network Config](./shared.md) — Native token details and precompiles
- [Testnet Config](./testnet.md) — Testnet endpoints and faucet URL
- [Mainnet Config](./mainnet.md) — Mainnet endpoints and production parameters
