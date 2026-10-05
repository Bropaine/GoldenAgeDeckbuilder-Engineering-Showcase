# Persistence: restore facts without replaying decisions

[Back to showcase](../README.md) · [Restore-flow diagram](../diagrams/RESTORE_FLOW.md)

## Problem

A seed and an encounter cursor cannot describe a partially completed deckbuilder Run. The player may be in combat, choosing a reward, or deciding between recovery options. Regenerating that situation by running normal initialization risks drawing again, consuming randomness, applying a reward twice, or settling health twice.

## Decision

The accepted persistence model captures versioned authoritative state and reconstructs it directly. Capture follows the authoritative Run session and includes the supported durable Battle and pending-decision state. Transient hover, selection, subscriptions, animations, and Unity object references are rebuilt or cleared.

Canonical gameplay content identity is separate from display names, Unity pointers, and asset paths. Compatibility is governed by schema, rules, content, and RNG revisions; the visible application version is diagnostic information.

## Construct before replacing

Restore reads a candidate, checks compatibility and semantic invariants, builds a separate candidate state graph from saved facts, and installs it only after successful validation and construction. Failed load or candidate construction replaces no live authority.

The new graph receives fresh live bindings. Old UI requests and callbacks cannot mutate it merely because saved identifiers have the same values. Persistent identity survives; obsolete live references lose authority.

Ordinary gameplay commands are deliberately absent from reconstruction. Restore does not perform New Run, opening shuffle, draw, reward sampling, upgrade, advancement, or outcome settlement to reproduce the past.

## Randomness belongs to the save

Supported gameplay randomness has explicit durable algorithm/version and current-state ownership. Shuffle and reward sampling have separate streams. Resume continues the saved stream states rather than reconstructing them from a starting seed.

Developer retry has different semantics: it creates a fresh Battle from committed Run state using current streams and does not rewind randomness. Keeping retry and resume distinct prevents a convenience tool from defining player continuation behavior.

## Player-facing policy

The accepted capability set has one resumable Run slot and a hidden technical backup. Autosave occurs after accepted state-changing commands at a stable boundary, with a dirty-state flush at quit. Half-applied transitions are not valid snapshot boundaries.

Continue restores the current supported decision. Replacing resumable progress with New Run requires confirmation. Backup recovery requires explicit consent. Invalid or unsupported saves are retained and rejected rather than silently rewritten; failed saves are not reported as successful.

## Tradeoffs and failure scope

Direct snapshots require each new mutable gameplay domain to declare capture, restore, reset, and compatibility behavior. That cost is deliberate: adding a mechanic cannot silently extend persistence coverage.

Compatibility rejection is bounded protection, not indefinite migration. Backup recovery is not protection against total device loss, deletion of every copy, or corruption of every recovery copy. State after the last successfully committed snapshot can still be lost if a later save fails.

## Inspect the implementation boundaries

Two [selected production exhibits](../samples/README.md) connect these decisions to source inspected October 5, 2026:

- [Durable save commit](../samples/DURABLE_SAVE_COMMIT.md) shows validation of temporary-file read-back, failure reporting, and the point where durability becomes clean.
- [Staged session restore](../samples/STAGED_SESSION_RESTORE.md) shows early rejection, preparation before installation, a narrow assignment seam, and assertions that rejection preserves live owners.

These are non-standalone excerpts with the real save format and reconstruction internals omitted. The selected test source was inspected, not executed. Synchronous session installation and filesystem commit are separate boundaries; the former does not independently prove crash-safe storage.

## Recorded outcome

The September 23 acceptance record reports clean-launch/filesystem verification of active combat, pending reward, pending recovery, backup recovery, failed-load safety, fresh bindings, and honest save failures. It also records the 1,681 / 1,681 EditMode regression checkpoint.

These results apply to the implemented capability set. Cloud saves, profile progression, future maps, shops, relics, and arbitrary future migrations are outside the persistence claim. [Verification and limitations](TESTING_AND_VERIFICATION.md).
