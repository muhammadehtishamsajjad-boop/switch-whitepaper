# Push Settlement

Selection determines _what_ is bought. Settlement determines _who receives it_, and is where most reward mechanisms leak value.

## 5.1 Time-weighted balance

A point-in-time snapshot at the hour boundary is trivially gameable: an address can acquire a large balance seconds before the boundary, receive a full hour's allocation, and exit immediately after. The cost of the attack is one round-trip of slippage; the payoff is the entire pro-rata share of an hour of fees.

Allocation is therefore computed on the holder's **time-weighted balance** over the round, reconstructed from transfer events:

```
B̄_j(t) = (1 / T) · Σ_k B_j(k) · Δ_k
```

> Where **B_j(k)** is holder **j**'s balance during interval **k**, **Δ_k** is that interval's duration, and **T** is the round length. An address held for six minutes of a sixty-minute round earns one tenth the weight of an address held throughout.

This makes the sniping attack unprofitable by construction rather than by detection: there is no threshold to tune and no heuristic to evade, because the reward is simply proportional to time held.

## 5.2 Allocation

Let `Q` be the quantity of the winning instrument actually acquired, and `H(t)` the set of holders with non-zero time-weighted balance:

```
a_j = ⌊ Q · B̄_j / Σ_{h ∈ H} B̄_h ⌋
```

Allocations are floored to the instrument's smallest representable unit. The rounding remainder `Q − Σ a_j` is not retained, redirected, or swept to any address — it is carried into the next round's distributable quantity. Over time the remainder nets to approximately zero rather than accumulating anywhere.

Balances held by the pool, the lock contract, and the distributor itself are excluded from `H(t)`. Including them would route a share of every distribution back to protocol-controlled addresses, which is the same value leak the design is meant to avoid.

## 5.3 Merkle commitment

Each round produces a Merkle tree over `(address, amount)` leaves. The root is committed to `SwitchRewards` together with the round's complete score table. Committing the score table matters as much as the root: it is what lets a third party verify the winner was the winner, not merely that the payout matched the root.

The tree is used for verification and dispute, not for claiming. Settlement is a push.

## 5.4 Execution and slippage

Acquisition executes against the best available venue for the winning instrument, subject to a hard bound:

```
P_exec ≤ P_oracle · (1 + ε)
```

> Where **P_oracle** is the Chainlink reference at execution and **ε** is the maximum tolerated slippage. If the bound cannot be met, the round does not execute and the budget rolls forward. The protocol will decline a round rather than accept an arbitrary fill — a rolled budget is recoverable, an overpaid fill is not.

## 5.5 Risk model

| Risk | Exposure and mitigation |
|---|---|
| **Worker liveness** | If the scoring worker halts, rounds do not execute. Budgets accrue in the hook and remain distributable when service resumes. Capital is not at risk; distribution is delayed. Redundancy is addressed in §8. |
| **Oracle failure** | A stale or deviating feed removes the instrument from `E(t)` rather than producing a mispriced purchase. A market-wide feed outage empties the eligible set, which triggers a rolled round. |
| **Issuer risk** | Distributed assets are tokenized equities issued by third parties. Switch does not custody, back, or guarantee them, and inherits their redemption and solvency assumptions in full. |
| **Trend-following drawdown** | A momentum-weighted rule buys strength and will therefore buy local tops. The score floor bounds the worst case per round; it does not eliminate it. The engine optimises for a reproducible rule, not for risk-adjusted return. |
| **Volume dependence** | Distribution size is a function of trading volume. Low-volume periods produce small distributions. There is no reserve, no smoothing, and no minimum payout. |
| **Immutability** | The absence of admin keys is symmetric: a discovered flaw cannot be patched. There is no upgrade path, no pause, and no recovery function. This is a deliberate trade, not an oversight. |
