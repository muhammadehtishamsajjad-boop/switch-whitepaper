# Protocol Architecture

Switch is four immutable contracts on Robinhood Chain (chain ID 4663) plus an off-chain scoring worker whose output is fully reproducible from public data. The worker cannot choose a winner; it can only compute the one the formula already determines, and any observer can detect a deviation.

## 3.1 Contracts

| Contract | Responsibility |
|---|---|
| SwitchToken | ERC-20. Fixed supply of 1,000,000,000. Enforces the 1.5% maximum wallet on transfer. No mint, no burn authority, no pause, no blacklist. |
| SwitchHook | Uniswap v4 hook. Captures 3% of each swap in WETH at execution and splits it across three immutable destinations compiled into the bytecode. |
| SwitchLock | Holds protocol-owned liquidity. Enforces a rolling 60-day lock on each deposited position; no early-withdrawal path exists. |
| SwitchRewards | Merkle distributor. Accepts an hourly root, and pushes the round's equity allocation to holder addresses in batches. |

## 3.2 The hourly cycle

1. **Fee accrual** — Throughout the hour, every swap routed through the pool contributes 3% in WETH. 60% of that accrues to the round's purchase budget.
   `SwitchHook → budget`
1. **Close of hour** — The budget is sealed at the hour boundary. Fees arriving after the boundary belong to the next round, not this one.
   `B(t) fixed at t = hh:00:00 UTC`
1. **Scoring** — Every instrument in the eligible set is scored on the hour that just closed. The highest score wins. Ties resolve deterministically.
   `w(t) = argmax S_i(t)`
1. **Acquisition** — The full budget is used to acquire the winner, subject to a slippage bound. A failed or bounded-out execution rolls the budget forward.
   `Q = quantity acquired`
1. **Allocation & commitment** — Q is divided pro rata by time-weighted balance over the hour. The resulting Merkle root is committed on-chain alongside the full score table.
   `root(t) → SwitchRewards`
1. **Push settlement** — Allocations are pushed to holder addresses in batches. Gas is paid from the operations allocation. The holder does nothing.
   `transfer → holder`

## 3.3 Trust boundaries

It is worth being precise about what must be trusted and what must not.

| | |
|---|---|
| **Requires no trust** | Fee capture and splitting, the wallet cap, the liquidity lock, supply integrity. These are enforced by immutable bytecode with no privileged caller. |
| **Verifiable, not trusted** | Winner selection. The worker computes it, but the inputs and the function are public — any third party can recompute every historical round and prove a mismatch. |
| **Requires trust** | Liveness. If the worker halts, rounds do not execute and budgets accumulate unspent. Funds are not at risk, but distribution pauses. This is the protocol's principal centralisation surface and is addressed in §8. |
| **External dependency** | Chainlink price feeds and the underlying tokenized-equity issuers. Switch inherits the liveness and solvency assumptions of both. |
