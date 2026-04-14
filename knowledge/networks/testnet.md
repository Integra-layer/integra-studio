# Integra Testnet

## EVM Configuration

| Property | Value |
|----------|-------|
| Chain Name | `Integra Testnet` |
| Chain ID (decimal) | `26218` |
| Chain ID (hex) | `0x666A` |
| EVM JSON-RPC | `https://testnet.integralayer.com/evm` |
| WebSocket | `wss://testnet.integralayer.com/evm/ws` |
| Block Explorer (EVM) | `https://blockscout.integralayer.com` |

## Cosmos Configuration

| Property | Value |
|----------|-------|
| Cosmos Chain ID | `integra-testnet-1` |
| Cosmos RPC | `https://testnet.integralayer.com/rpc` |
| REST API | `https://testnet.integralayer.com/api` |
| gRPC | `testnet.integralayer.com:9090` |
| Block Explorer (Cosmos) | `https://scan.integralayer.com` |

## Gas

| Property | Value |
|----------|-------|
| Min Gas Price | `0 airl (free on testnet)` |
| Block Time | `~5s` |
| Finality | `Instant (CometBFT DPoS)` |

## Web3Auth

| Property | Value |
|----------|-------|
| Network | `sapphire_devnet` |
| Client ID | `(from .env -- per-project)` |

## Faucet

| Property | Value |
|----------|-------|
| URL | `https://testnet.integralayer.com` (also via `https://docs.integralayer.com` → switch to testnet) |
| Amount per request | `10 IRL + 1,000 tUSDI` |
| Cooldown | `24 hours per address` |

> For token info, WIRL address, precompiles, and decimal warnings, see [shared.md](./shared.md).
