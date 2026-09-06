# Roadmap

Milestones for the Switchboard, push settlement, and the public round log.

1. **Phase 1 — Foundation** — Launch the Switch site and grow the community. Independent audit of all four contracts with the report published in full, including unremediated findings. Deployment, liquidity seeding, and the first lock. Ticker and destination addresses published before launch.
1. **Phase 2 — Rounds and the public log** — The first hourly rounds run on Robinhood Chain. Every round is published with its complete score table: all eligible instruments, all four normalised components, the composite, the winner, the quantity acquired, and the Merkle root. Permanent and machine-readable.
1. **Phase 3 — Open verification** — An open-source recomputation client that rebuilds any historical round from public inputs and diffs it against what the protocol did. Verification stops being a claim in a document and becomes a command anyone can run.
1. **Phase 4 — Distributed workers** — Addressing the liveness dependency in §3.3: multiple independent workers computing the same deterministic output, with settlement requiring agreement. Because the function is deterministic, agreement is a comparison, not a consensus problem.

Note what this roadmap does not contain: contract upgrades. Any change to weights, cadence, or the fee split requires a new deployment, published as a separate specification, that holders may adopt or ignore — the original contracts keep running untouched.
