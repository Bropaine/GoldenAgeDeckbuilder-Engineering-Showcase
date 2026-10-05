# Architecture: state ownership follows lifetime

[Back to showcase](../README.md) · [Lifetime diagram](../diagrams/STATE_LIFETIMES.md)

The architectural decision is to give reusable content, persistent Run values, and live combat separate owners. Focused C# rules act on those owners; presentation consumes authoritative state and completed-command results.

## Responsibility boundaries

| Layer | Responsibility | Lifetime |
|---|---|---|
| Authored definitions | Reusable card, encounter, and Run content | Shared read-only content |
| Run state | Persistent card membership, committed health, progression, pending decisions | One Run |
| Battle state | Live combatants, resources, intents, cards, zones, turn and outcome | One Battle |
| Rules and services | Validate and perform supported commands | Operate on explicitly supplied state |
| Presenters | Read state, format information, forward bound requests | Rebound when live state changes |
| Unity views and composition | Input, scene references, visual lifetime, orchestration, feedback | Presentation/session scope |

Definitions are immutable runtime inputs. Run state does not retain live combat objects. A fresh Battle receives current persistent values and newly constructed mutable objects. Persistent deck changes affect subsequent fresh Battles rather than modifying an already-created Battle in place.

## One value, different authorities

Health illustrates why ownership matters. During combat, live Battle health is authoritative. After validated completion, a settlement service commits the surviving value to the Run exactly once. The next fresh Battle reads that committed value.

Continuous synchronization would blur the boundary: an abandoned or restarted Battle could accidentally commit damage, and a presentation callback could appear to own progression. Explicit settlement gives the transition a place to validate and a result to inspect.

## Commands and feedback

The normal flow is a bound request, runtime validation, authoritative mutation, and a completed result. A presenter reads current totals; feedback reads what the command actually did. Those are different questions.

For example, an applied status grant is an event amount, while the HUD displays the current total. Inferring the grant from a pair of UI snapshots becomes unreliable when multiple changes occur. A result records it directly.

Animation and audio callbacks do not apply damage, settle outcomes, or advance encounters. Interrupted feedback therefore does not revoke a completed gameplay command.

## Dependencies and scope

The documented assembly boundaries separate definitions, runtime logic, Unity authoring/conversion, presentation, and tests. Dependencies are explicit. The accepted architecture uses focused services rather than a speculative universal effect engine or combatant framework.

This is a scope decision with a tradeoff: a future mechanic may require a deliberate extension and new verification. The benefit is that today's ownership and ordering remain inspectable without a framework built around hypothetical mechanics.

## Risks addressed

| Risk | Architectural response |
|---|---|
| A new encounter inherits old combat status | Fresh mutable Battle objects |
| A stale click acts on a replacement session | Commands carry exact context; runtime rejects stale bindings |
| A duplicated card cannot be distinguished | Persistent per-copy identity |
| A delayed effect decides whether damage happened | Gameplay completes independently of presentation playback |
| Save/load initializes over restored facts | Separate direct reconstruction path |

The accepted-state record supports these responsibility boundaries. This showcase does not include the private implementation or claim a new source audit. [Verification scope](TESTING_AND_VERIFICATION.md).
