# Integra Shared Network Configuration

## Native Token

| Property | Value |
|----------|-------|
| Name | `IRL` |
| Symbol | `IRL` |
| Smallest Unit | `airl` |
| EVM Decimals | `18` |
| Cosmos SDK Decimals | `6` |

> **CRITICAL: Dual Decimal Warning**
> EVM layer uses 18 decimals (1 IRL = 10^18 airl). Cosmos SDK Bank module uses 6 decimals.
> Any code bridging between EVM and Cosmos MUST explicitly convert. Failing to convert will
> result in mass-scale token value errors (1,000,000x too much or too little).

## WIRL (Wrapped IRL)

| Property | Value |
|----------|-------|
| Address | `0x5002000000000000000000000000000000000001` |
| Standard | `ERC-20` |
| Description | `Wrapped version of native IRL for use in EVM smart contracts` |

## Precompiled Contracts

| Name | Address | Purpose |
|------|---------|---------|
| Staking | `0x0000000000000000000000000000000000000800` | Delegate/undelegate/redelegate |
| Distribution | `0x0000000000000000000000000000000000000801` | Claim staking rewards |
| ICS-20 | `0x0000000000000000000000000000000000000802` | IBC token transfers |
| Vesting | `0x0000000000000000000000000000000000000803` | Vesting account management |
| Authorization (authz) | `0x0000000000000000000000000000000000000804` | Grant/revoke authorizations |
| Bank | `0x0000000000000000000000000000000000000805` | Native token transfers |
| Governance | `0x0000000000000000000000000000000000000806` | Submit/vote on proposals |
| Slashing | `0x0000000000000000000000000000000000000807` | Validator slashing queries |
| Evidence | `0x0000000000000000000000000000000000000808` | Double-sign evidence |

## Consensus

| Property | Value |
|----------|-------|
| Engine | `CometBFT (DPoS)` |
| Block Time | `~5 seconds` |
| Finality | `Instant (single-slot)` |
| SDK Version | `Cosmos SDK v0.53.5` |
| EVM Version | `go-ethereum v1.15.11` |

> For network-specific endpoints, chain IDs, and gas config, see [mainnet.md](./mainnet.md) or [testnet.md](./testnet.md).
