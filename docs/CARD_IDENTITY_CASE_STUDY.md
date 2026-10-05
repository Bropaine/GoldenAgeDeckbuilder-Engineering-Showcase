# Persistent card identity: one definition, several independent copies

[Back to showcase](../README.md) · [State lifetimes](../diagrams/STATE_LIFETIMES.md)

## Problem

Two cards can share identical authored rules while representing different player-owned copies. Upgrading or removing “the card with this definition” becomes ambiguous. Using deck position as identity also fails when membership changes.

## Decision

The project separates three identities:

| Identity | Meaning | Scope |
|---|---|---|
| Content definition | Reusable authored card rules | Content catalog |
| Persistent card copy | One exact player-owned entry and its upgrade state | Owning Run |
| Battle instance | One fresh mutable card moving through combat zones | Owning Battle |

Persistent copy identity is stable and non-positional within its owning Run. Equal raw identifiers in another Run do not identify the same copy. Each fresh Battle creates fresh card instances from current effective definitions and retains an origin link to the persistent copy.

## Illustrative duplicate-card scenario

Consider two copies of the same basic card, A and B. An ordinary upgrade targets A. A retains its identity and deck position, while its effective authored definition changes. B remains unchanged.

A later fresh Battle creates new runtime instances for both. The instance originating from A uses upgraded content; the one originating from B uses base content. Neither reuses the prior Battle's mutable card object.

The labels A and B are illustrative, not production identifiers or serialized examples.

## Removal and identity retirement

Exact-copy removal targets one persistent entry. Surviving identities, relative order, and upgrade state remain intact. Removed identifiers are retired within that Run and are not allocated again.

Save/restore preserves the allocation frontier explicitly. Inferring it only from surviving entries would permit reuse after a high-numbered copy had been removed, making an old request capable of identifying the wrong future copy.

Deck revision supplies an additional stale-state guard for refinement requests. Expected failures validate before mutation; successful persistent changes increment the revision once.

## Lifetime proof

The documented card-lifetime verification covers duplicate upgrades across fresh Battles and retry, fresh reward entries, exact removal, survivor ordering, stale old-Run requests, identity retirement, and once-only health settlement. The later accepted persistence milestone preserves those identities and upgrade states across save/restore.

This keeps two guarantees compatible: the Run remembers the player's choices, while the Battle remains a fresh mutable simulation.

## Boundaries

The accepted refinement scope includes ordinary single-level proof upgrades and an exact-copy removal service. It does not establish branching upgrades, arbitrary transformations, a generalized mutation framework, or a player-facing free-removal source. Service acceptance should not be presented as evidence of a shipped shop or removal screen.

The public explanation omits allocator implementation, production APIs, and card-balance tables. [Verification scope](TESTING_AND_VERIFICATION.md).
