# Conclusion

Two design decisions carry this protocol, and both are subtractive.

The first is that **rewards are denominated in assets the protocol does not issue.** This forecloses the reflexive loop described in §2.1. Switch cannot inflate its way to a headline yield, because it cannot print what it distributes. Distribution volume falls when trading falls — but the mechanism does not depend on new capital to make prior distributions good, because they were never denominated in a claim on the protocol.

The second is that **selection is arithmetic and settlement is a push.** There is no manager to trust, no mandate to renegotiate, no key to compromise, and no claim transaction to remember. What the protocol will do in any given hour is a published function of public inputs; what it did in every past hour can be recomputed and checked by anyone.

Neither decision is free. Immutability means a discovered flaw cannot be patched. A deterministic momentum rule will buy local tops, and will do so visibly, on the record, in full public view. Volume-linked distribution means quiet weeks pay little. These costs are accepted deliberately, and they are stated here rather than discovered later — because the entire proposition is that the protocol does what it says, and a document that oversold it would be the first counterexample.

Most protocols pay you in their own token, and ask you to claim it. Switch pays in assets it did not create, and delivers them while you do nothing.
