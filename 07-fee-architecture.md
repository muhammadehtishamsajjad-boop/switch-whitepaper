# Fee Architecture

Fee capture occurs inside a Uniswap v4 hook at swap execution, denominated in WETH. There is no separate collection transaction, no accrual the protocol must remember to sweep, and no window during which captured fees sit in an address that anyone controls.

| Share | Of swap | Destination and constraint |
|---|---|---|
| 60% | 1.80% | **Hourly acquisition budget.** Funds the round's purchase of the winning instrument. Cannot be redirected; unspent budget rolls forward and is never swept. |
| 30% | 0.90% | **Protocol-owned liquidity.** Paired and deposited to `SwitchLock` under a rolling 60-day lock with no early-withdrawal path. |
| 10% | 0.30% | **Operations.** Infrastructure, the scoring worker, and the gas for every push distribution. This is what makes settlement free at the point of receipt. |

## 7.1 Immutability of destinations

The three destination addresses are compiled into the hook's bytecode as constants. They are not stored in mutable state, not settable by a constructor argument after deployment, and not reachable by any function. Redirecting fees would require deploying a different contract and convincing the market to trade against it — which is visible, permissionless to detect, and requires no trust in the original deployment.

## 7.2 Token constraints

| | |
|---|---|
| **Supply** | 1,000,000,000, fixed. No mint function exists. |
| **Maximum wallet** | 1.5% of supply, enforced on transfer. Limits the share of any single distribution routable to one address. |
| **Transfer tax** | None. Wallet-to-wallet transfers are untaxed; the 3% applies only to pool swaps. |
| **Privileged roles** | None. No owner, no pauser, no blacklist, no proxy, no implementation pointer. |

The wallet cap interacts with §5.2 in a way worth making explicit: because no address can exceed 1.5% of supply, no address can receive more than approximately 1.5% of any hourly distribution. The cap is a distribution-fairness mechanism as much as a concentration limit.
