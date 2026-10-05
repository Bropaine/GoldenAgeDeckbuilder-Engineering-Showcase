# Project overview

[Back to showcase](../README.md)

Golden Age Comics Roguelike Deckbuilder combines turn-based card combat with a comic-book presentation centered on Black Terror. Its accepted vertical slice contains a short linear Run with combat, card rewards, recovery, and terminal win/loss states. Later accepted milestones add individual persistent card copies and durable continuation.

## The engineering problem

A deckbuilder has several overlapping lifetimes. A card's authored rules are reusable content. The player's copy belongs to a particular Run. Its Hand position, transient combat identity, and zone membership belong to a particular Battle. Treating all of these as one object creates opportunities for accidental persistence, lost upgrades, or stale inputs affecting a replacement session.

The project models those boundaries explicitly, then carries them into commands, presentation, and persistence. A useful review question is whether each system can answer: who owns this state, how long does it live, and what evidence proves this transition?

## What the checkpoint demonstrates

| Area | Accepted/documented scope | Important boundary |
|---|---|---|
| Run lifecycle | Linear progression, rewards, recovery, win/loss | Broader maps, shops, events, and metagame are outside the showcased scope |
| Multi-enemy foundation | Ordered 1–3 enemy runtime state and targeting | Two-/three-enemy behavior is primarily proven by fixtures; production route content remained single-enemy |
| Deck refinement | Persistent copies, ordinary single-level upgrade proof, exact-copy removal service | Removal service acceptance does not establish a shipped free-removal UI or shop |
| Persistence | Resume the supported Run and decision state | No promise of indefinite future compatibility or cloud storage |
| Presentation | Repeated enemy units, exact inspection, help, feedback | Art completeness and broad accessibility certification are unclaimed |
| Animation tools | Capabilities described in a production manual | No tooling test total or later acceptance checkpoint is supplied here |

## Why these case studies are useful

For gameplay engineering, the project offers concrete examples of identity scope, validation, deterministic turn execution, and lifetime-safe commands. For tools engineering, it offers asset ownership, regeneration, and editorial configuration. For general software engineering, it offers reconstruction of authoritative state, compatibility boundaries, and evidence tied to exact checkpoints.

The public documentation explains these decisions at a responsibility level. It omits production schemas, source maps, content tables, private contracts, and algorithms that would expose a substantial reconstruction path.

## Evidence basis

The summaries draw from the supplied accepted-state record, the accepted persistence contract, the card-lifetime proof, the approved visual standard, the animation manual, and project workflow rules. Those private documents are not redistributed. The [verification page](TESTING_AND_VERIFICATION.md) identifies the public evidence scope and limitations; diagrams are conceptual explanations, not generated code diagrams.
