# The Problem

## 2.1 Reflexive yield

The dominant reward primitive in on-chain markets is redistribution denominated in the protocol's own token. A holder receives more units of the thing they already hold. Whether that constitutes yield depends entirely on whether the token's price holds — which depends on continued net inflow, which the distribution itself is supposed to attract.

The circularity is not incidental; it is the mechanism. Nominal APR can be set arbitrarily high because it is denominated in a unit the protocol mints or recycles at will. Real return to the holder is:

```
r_real = (1 + r_nominal) × (P_t+1 / P_t) − 1
```

> Where **r_nominal** is the advertised emission rate and **P** is the token's own price. Any emission funded by dilution pushes the second term down as it pushes the first term up. The two are not independent variables.

A distribution denominated in an asset the protocol does not issue breaks the coupling. The value delivered is exogenous: it does not depend on the protocol's own price, cannot be inflated by emitting more of it, and does not reverse when inflows slow. Distribution volume falls with trading activity — but what has already been delivered stays delivered.

## 2.2 The claim tax

Claim-gated rewards impose a fixed cost on a variable benefit. A holder claims only when

```
E[value_claim] > gas_cost + attention_cost
```

Because gas is roughly constant regardless of position size, the threshold is regressive: it is trivially cleared by large holders and frequently unmet by small ones. The result is a systematic transfer from small and inattentive holders to large and automated ones — before any consideration of the mechanism's stated distribution.

Push settlement removes the inequality entirely. If the protocol delivers the asset and pays the gas, there is no threshold to clear, no expiry to miss, and no dust left stranded in a distributor contract.

## 2.3 Discretion as liability

A protocol that buys real assets must decide _which_ assets. The conventional answer is a treasury with a mandate and a multisig. This introduces three liabilities that are frequently understated:

- **Key risk.** Any address that can direct purchases can direct them elsewhere. Multisig reduces the probability of misuse; it does not remove the capability.
- **Mandate drift.** A discretionary mandate is renegotiated under stress, which is precisely when holders are least able to exit.
- **Unverifiability.** Holders cannot check a judgement. They can only check an outcome, after the fact, with no counterfactual.

A deterministic rule inverts all three. If selection is a published function of public inputs, there is no key to compromise, no mandate to renegotiate, and every past decision can be independently recomputed and verified against what the protocol actually did.
