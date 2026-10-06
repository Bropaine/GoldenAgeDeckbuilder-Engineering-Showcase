# Testing and verification: evidence has a date and a scope

[Back to showcase](../README.md)

**This repository does not run or distribute the private game's test suite.** The results below are inherited documentary evidence for identified accepted checkpoints. No new gameplay tests, build, or playtest was performed to create this showcase.

## Selected source inspection

On October 5, 2026, selected production files and related test methods were inspected to prepare the [source exhibits](../samples/README.md). The displayed fragments are exact excerpts of that reviewed source. This was a bounded inspection, not a full source audit.

The displayed test methods were not executed for the showcase. Their assertions illustrate intended properties; their presence alone is not a passing result. The reviewed source is later than the historical accepted checkpoints below, so those totals must not be presented as verification of the exhibited source state.

## Recorded checkpoints

| Checkpoint | Recorded evidence | Scope |
|---|---|---|
| Vertical slice, accepted September 14, 2026 | 1,247 / 1,247 EditMode tests; matching Rider result; standalone acceptance | Historical slice with launch, progression, rewards, recovery, win/loss and input checks |
| Individual card identity/refinement, accepted September 21, 2026 | 1,468 / 1,468 EditMode tests; zero failures; packaged acceptance | Historical copy-identity and refinement milestone |
| Durable continuation, accepted September 23, 2026 | **1,681 / 1,681 EditMode tests; zero failures; packaged clean-launch/filesystem acceptance** | Latest accepted gameplay checkpoint described in the supplied state record |
| Animation pipeline | Production manual describes workflows and checks; October 5 source inspection covers selected ownership/editorial boundaries | Documented and inspected scope; no supplied tooling acceptance result, executed showcase tests, or test total |

The totals are checkpoints of an evolving suite, not independent totals to add together. They do not establish coverage percentages, performance benchmarks, universal correctness, or fresh verification of later source.

The state record's header labels its September 23 reconciliation revision a candidate while its body explicitly records the accepted milestones. This showcase preserves those dated acceptance statements; it does not claim the reconciliation revision itself was separately approved.

## What the persistence acceptance exercised

The recorded Owner-performed standalone scenarios include launch without resumable state; active-Battle quit/relaunch/Continue; pending reward and recovery decisions; fresh restored bindings; explicit recovery from a valid backup when the primary is unusable; failed or unsupported load without silent session replacement; and save failures without false success.

The record reports a reviewed standalone log with no persistence/runtime error at that checkpoint. It separately retains a nonblocking ComputeBuffer disposal diagnostic at shutdown. “No persistence error observed” should not be expanded into “the build emitted no diagnostics.”

## Verification strategy

The documented engineering approach combines focused C# tests with integration and packaged/manual evidence. Important properties include:

- Failed commands preserve state rather than partly spending resources or applying effects.
- Foreign or stale Run/Battle requests cannot mutate current authority.
- Card copies preserve identity and upgrades across fresh Battle boundaries.
- Sequential combat results preserve only completed actions and stop correctly on defeat.
- Resume preserves supported state and RNG continuity relative to uninterrupted execution.
- Storage and recovery behavior is exercised outside the Unity Editor.

These are summarized proof themes. The exhibits include selected test methods and assertion fragments, including corrupt temporary-file read-back, invalid restore rejection, and no-op editorial playback preservation; the complete private suite and raw reports remain private.

## Material limits

| Area | What the supplied evidence establishes | What remains unclaimed |
|---|---|---|
| Multi-enemy | Automated/manual two-/three-enemy proof fixtures and accepted foundation | Broad multi-enemy production-content completion |
| Playtesting | One independent fresh-player session, plus an internal familiar-player session | A second independent session or broad user study |
| Input | Mouse/pointer click and hover | Keyboard/controller support |
| Display | Specific accepted windowed and exclusive-fullscreen modes, including one 4K exclusive-fullscreen check | Every 4K mode or native 4K borderless guarantee |
| Persistence | Implemented Run/Battle/decision capability set | Cloud saves, metagame, future mechanics, indefinite migration |
| Tooling | Manual-backed description | Tool acceptance inferred from the gameplay regression total |
| Quality measurement | Recorded checks and observations | Test coverage percentage, frame-time benchmark, crash-rate statistic |

## Evidence policy for future edits

A new claim should identify its checkpoint, evidence type, execution environment, result, and limits. A new screenshot demonstrates the captured presentation; it does not independently prove save correctness or determinism. A representative code sample demonstrates that sample; its passing tests do not certify private production code.

Descriptions of future work belong in a plan until verified. Accepted historical evidence stays dated when the project advances.
