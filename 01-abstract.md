# Abstract

Token holders today face a binary choice: hold a reward token that pays in more of itself, or hold a productive asset and forfeit any claim on protocol revenue. Reward tokens denominated in their own supply are reflexive by construction — the yield is funded by the next buyer, and it evaporates the moment inflows stop. The alternative, a discretionary treasury that acquires real assets on holders' behalf, replaces one problem with another: it requires a manager, a mandate, and a set of privileged keys that can be turned against the people they were built for.

Separately, reward distribution is almost universally claim-gated. Holders must submit a transaction, pay gas, and remember to do it. Claims that cost more than they return are never made, and the unclaimed remainder accrues to whoever is large or sophisticated enough to bother. A mechanism advertised as passive is, in practice, a tax on inattention.

Switch addresses both as a single autonomous system on Robinhood Chain:

1. **The Switchboard** — a deterministic selection engine that scores every eligible tokenized equity once per hour against a fixed, public formula and commits the round's entire purchase budget to the single highest-scoring instrument. No manager, no mandate, no discretion.
2. **Push settlement** — a Merkle-committed distribution path that delivers the purchased equity directly into holder balances, with gas paid by the protocol. There is nothing to claim, stake, or lock; holding is the entire action.

The thesis is simple: **rewards should be denominated in assets the protocol does not issue, and selection should be arithmetic anyone can reproduce.** This document specifies the mechanism design, the mathematics governing scoring and settlement, and the risk model underlying both systems.
