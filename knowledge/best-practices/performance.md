# Performance Optimization Best Practices

Guidelines for building fast, efficient dApps on the Integra ecosystem.

---

## 1. Frontend Performance

### Next.js Optimization
- **Server Components by default**: Only use `"use client"` when you need interactivity (clicks, state, effects)
- **Suspense boundaries**: Wrap async components with `<Suspense fallback={<Skeleton />}>` for progressive loading
- **Dynamic imports**: `next/dynamic` for heavy components (charts, editors, code blocks)
- **Image optimization**: Always use `next/image` with explicit width/height to prevent CLS
- **Route prefetching**: Next.js prefetches `<Link>` routes automatically — leverage this for navigation speed

### Bundle Size
- **Tree-shaking**: Import specific functions, not entire libraries (`import { formatEther } from 'viem'` not `import * as viem`)
- **Analyze bundles**: Use `@next/bundle-analyzer` to identify bloat
- **Avoid large dependencies** for small tasks (don't import moment.js for a single date format)
- **Code splitting**: Each page should be its own chunk. Heavy features in separate dynamic imports
- **Target**: Initial bundle under 200KB gzipped for fast first load

### Rendering Performance
- **Avoid unnecessary re-renders**: Use `React.memo` for expensive components, `useMemo`/`useCallback` for stable references
- **Virtual lists**: For lists >50 items, use `react-virtual` or `tanstack-virtual`
- **Debounce inputs**: Search/filter inputs should debounce (300ms) before triggering queries
- **Stable keys**: Never use array index as React key for dynamic lists

### Asset Optimization
- **WebP** for images with JPEG fallback
- **SVG** for icons and logos (inline or sprite sheet)
- **Font loading**: `font-display: swap` for web fonts. Preload critical fonts
- **Lazy load below-fold**: Images, charts, and heavy components below the viewport

---

## 2. Smart Contract Gas Optimization

### Storage Patterns
- **Pack state variables**: Group `uint128 + uint128` into a single 256-bit slot instead of two `uint256`
- **Use mappings over arrays** when you don't need iteration
- **Cache storage reads**: Read a storage variable into memory if used more than once in a function
- **Delete unused storage**: `delete` refunds gas (up to 50% of the operation cost)

### Function Optimization
- **Custom errors** over require strings: `error InsufficientBalance(uint256 available, uint256 required)` saves ~50 bytes per error
- **Use `unchecked` blocks** for math that provably can't overflow (loop counters, post-validation)
- **Short-circuit conditions**: Put the cheapest check first in `require` chains
- **Avoid redundant checks**: Trust internal state that you control
- **External over public**: If a function is never called internally, use `external` (cheaper calldata)

### Event Optimization
- **Index up to 3 parameters** in events for efficient filtering
- **Use events for data logging** instead of storage when data only needs to be read off-chain
- **Batch operations** when possible (batch mint, batch transfer)

---

## 3. Web3 Communication

### RPC Optimization
- **Batch reads**: Use multicall patterns to combine multiple contract reads into one RPC call
- **Cache read results**: Use wagmi's built-in caching. Set `staleTime` appropriate to data freshness needs
- **Poll intervals**: Don't poll every block. Use 10-30s for non-critical data, event-driven for critical data
- **WebSocket for real-time**: Use WebSocket RPC for price feeds, order book updates, and transaction monitoring

### Transaction Optimization
- **Estimate gas before sending**: Show users the expected cost before they confirm
- **Gas price strategy**: Use network-suggested gas price (no overpaying on Integra's low-gas testnet)
- **Batch approvals**: ERC-20 `approve` once with max amount, or use `permit` for gasless approvals
- **Optimistic updates**: Update UI immediately on send, revert only on failure

---

## 4. Core Web Vitals Targets

| Metric | Target | How |
|--------|--------|-----|
| LCP (Largest Contentful Paint) | < 2.5s | SSR/SSG for above-fold, preload critical resources |
| FID (First Input Delay) | < 100ms | Minimize main-thread work, split heavy JS |
| CLS (Cumulative Layout Shift) | < 0.1 | Set explicit dimensions, use skeleton loaders |
| INP (Interaction to Next Paint) | < 200ms | Event handlers should be fast, offload heavy work |

---

## References

- `knowledge/conventions.md` — Solidity and TypeScript coding standards
- `knowledge/best-practices/ui-ux-design.md` — UX patterns that affect perceived performance
- `knowledge/networks/shared.md` — Integra block time and finality details
