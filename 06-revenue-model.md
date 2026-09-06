# Revenue Model

Switch has exactly one revenue source: the 3% fee captured on swaps through its own pool. There is no presale allocation to a treasury, no ongoing emission, and no secondary business line. Protocol revenue in hour `t` is:

```
R(t) = 0.03 · Vol(t)
B(t) = 0.60 · R(t) = 0.018 · Vol(t)
```

> Where **Vol(t)** is quote-denominated swap volume. Effectively 1.8% of all volume is converted into equities and distributed, 0.9% is converted into locked liquidity, and 0.3% funds operations including distribution gas.

The consequence worth stating plainly: **distribution scales with volume, not with a promised rate.** There is no target APR because there is no mechanism capable of targeting one. The table below is the formula above evaluated at four arbitrary inputs — it is multiplication, not a projection, and no row is more likely than any other.

| If daily volume were | Hourly buy budget | Buy budget over 24h | To POL over 24h | To operations over 24h |
|---|---|---|---|---|
| $250,000 | $188 | $4,500 | $2,250 | $750 |
| $1,000,000 | $750 | $18,000 | $9,000 | $3,000 |
| $5,000,000 | $3,750 | $90,000 | $45,000 | $15,000 |
| $10,000,000 | $7,500 | $180,000 | $90,000 | $30,000 |

{% hint style="info" %}
**Read this correctly**

These rows are deliberately not annualised, because annualising an arbitrary input produces a figure that reads as an expectation. They contain no estimate of what volume will be, no assumption that any level of volume persists, and no statement about what any individual address receives — that depends additionally on its share of supply and on how long the balance was held. They exist to make the mechanism legible, not to imply an outcome.
{% endhint %}

Note the second-order effect in the POL column. The 0.9% liquidity allocation compounds: deeper protocol-owned liquidity reduces price impact per trade, which supports larger trades, which increases the fee base that funds both distribution and further liquidity. The protocol is not dependent on third-party liquidity providers who can withdraw.
