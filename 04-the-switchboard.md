# The Switchboard

The Switchboard is the selection engine. It answers exactly one question per hour — which instrument receives the entire budget — and it answers it with arithmetic.

## 4.1 Eligible set

Let `E(t)` be the set of instruments eligible in round `t`. Membership requires all of:

- A live Chainlink price feed whose last update is within its declared heartbeat. A stale feed removes the instrument for that round rather than being extrapolated.
- Realised quote-side volume over the hour of at least `V_min`, excluding instruments too thin to absorb the budget without material impact.
- Available depth such that the budget represents no more than `δ_max` of quotable liquidity at the acquisition bound in §5.4.

Eligibility is evaluated per round. An instrument dropping out of `E(t)` has no effect on allocations already settled.

## 4.2 Metrics

For each instrument `i ∈ E(t)`, four raw quantities are measured over the hour that just closed:

```
m_i = (P_close − P_open) / P_open — momentum, the hourly return
p_i = V_buy / V_total — buy pressure, buyer share of volume
v_i = V_total — volume, quote-denominated
σ_i = stdev( r_1 … r_60 )
— realised volatility of minute returns
```

## 4.3 Normalisation

The four metrics are dimensionally incompatible — a return, a ratio, a currency amount and a standard deviation cannot be summed directly. Each is therefore converted to a cross-sectional z-score across `E(t)` and winsorised at ±3 to prevent a single outlier from dominating a round:

```
ẑ(x_i) = clamp( (x_i − μ_x) / s_x , −3, +3 )
```

> Where **μ_x** and **s_x** are the mean and sample standard deviation of metric **x** across the eligible set _in that round only_. No cross-round state enters the calculation, which is what makes each round independently reproducible from a single hour of public data.

Volume enters as `ln v` rather than `v`. Traded volume is heavy-tailed; without the log transform a single high-volume instrument would produce a z-score large enough to override the other three terms in every round it appeared.

## 4.4 Scoring and selection

```
S_i(t) = 0.40·ẑ(m_i) + 0.35·ẑ(p_i) + 0.15·ẑ(ln v_i) − 0.10·ẑ(σ_i)
w(t) = argmax_{i ∈ E(t)} S_i(t)
```

The weights encode a deliberate stance. Momentum and buy pressure together carry 0.75 — the engine is a trend follower, not a value screen. Volume at 0.15 acts primarily as a tie-breaker between instruments with similar price action. Volatility enters negatively, which means the engine prefers the calmer of two otherwise equivalent movers rather than chasing the most violent one.

Ties are resolved deterministically, never randomly: highest `ẑ(m)` first, then highest `ẑ(p)`, then ascending token address. Random tie-breaking would make historical rounds unverifiable, which would defeat the purpose of a public formula.

Note that no term in `S` references the protocol's own token, treasury balance, or prior winners. The engine cannot be steered by anyone's position, including the team's.

## 4.5 Degenerate rounds

A pure `argmax` always buys something, including in an hour when every eligible instrument fell. Two bounded rules address the degenerate cases without introducing discretion:

{% hint style="warning" %}
**Proposed — score floor**

If max S_i(t) < S_min, the round executes no purchase and the budget rolls into round t+1. The protocol declines to buy the best of a uniformly bad set. S_min is a constant compiled into the worker, not a tunable parameter.
{% endhint %}

{% hint style="warning" %}
**Proposed — repeat cap**

If instrument i has won the previous k consecutive rounds, it is excluded from E(t) for one round and the budget goes to the runner-up. This makes diversification structural rather than incidental: without it, the breadth of the resulting portfolio depends purely on market conditions.
{% endhint %}

Both rules are stateless in the sense that matters: they depend only on the published history of prior rounds, which anyone can read, so a round's outcome remains reproducible from public data alone.
