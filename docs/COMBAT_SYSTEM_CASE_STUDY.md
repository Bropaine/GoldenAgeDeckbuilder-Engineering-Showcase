# Combat: exact targets and ordered commands

[Back to showcase](../README.md) · [Command-flow diagram](../diagrams/COMBAT_COMMAND_FLOW.md)

## Problem

Moving from one enemy to multiple enemies affects identity, targeting, turn order, victory, and feedback. Selecting an enemy by list position or authored definition is insufficient: identical authored enemies may coexist, and the same local identifier may appear in a later Battle.

## Decision

The accepted foundation uses an ordered collection of 1–3 independent enemy runtime entries. Membership and order remain stable for the Battle. Each entry has Battle-local identity and its own mutable combat state; defeated entries remain in their slots.

A target is bound to the exact Battle context and one runtime enemy. The runtime validates that binding, membership, and liveness. A stale target cannot become valid merely because a replacement Battle contains an equal identifier.

## Validation before payment

Card play validates the active Battle, player turn, exact card membership in Hand, target, and mandatory costs before applying ordinary gameplay mutation. An invalid target therefore cannot consume resources or partially move a card.

The player-facing selection process can begin, cancel, or replace a pending target choice without spending anything. It carries a request to the runtime; it does not become an independent rules authority.

Expected gameplay failures use explicit failure results. Missing required configuration or broken invariants are programming/configuration errors. This distinction prevents routine invalid input from becoming exception-driven gameplay while keeping impossible states visible.

## Sequential enemy actions

Living enemies act in stable order. Each action records its exact actor and completed outcome. Defeated enemies do not act. Player defeat stops later actions; the result does not invent actions or a next player turn that never occurred.

Enemy-local Block and status timing belong to the acting enemy. Sequential attacks consume the same player-owned Block pool. Group victory requires the enemy group to be defeated, rather than testing a legacy single-enemy field.

The practical consequence is explainable combat: the system can report who acted, in what order, and why execution stopped.

## Illustrative failure scenario

Suppose a target was selected, then the Battle was replaced before the command reached the rules layer. Even if the new Battle has an enemy in the same visual slot, the request refers to the old context. Runtime rejection preserves resources, cards, and the new Battle's state.

This example illustrates the documented stale-target protection. It is not a reproduction script or a newly executed test.

## Tradeoffs and evidence

Fixed membership avoids introducing dynamic spawning, waves, or reorder semantics before they are needed. Focused enemy actions preserve existing rule ownership rather than building a generalized combatant hierarchy. A future dynamic lineup would require new lifetime and ordering contracts.

The accepted record describes automated and manual proof fixtures for two-/three-enemy targeting, ordered execution, group victory, shared player Block, and stale bindings. Production content in that record remained a four-encounter single-enemy route. This establishes a verified foundation at its checkpoint, not broad multi-enemy content completion. [Evidence details](TESTING_AND_VERIFICATION.md).
