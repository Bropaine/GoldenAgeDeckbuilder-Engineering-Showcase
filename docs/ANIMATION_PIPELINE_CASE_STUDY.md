# Animation tooling: regeneration preserves production work

[Back to showcase](../README.md)

**Evidence scope:** the wider workflow below is described by the supplied production animation manual. A bounded source inspection on October 5, 2026 separately supports the ownership and editorial-preservation exhibit. An Owner-provided October 5 recording separately demonstrates Story Beats V2 authoring. These evidence types establish different scopes; none is a fresh tooling acceptance report or new build/test result.

## Recorded cinematic editor demonstration

The [Story Beats V2 walkthrough](../media/STORY_BEATS_EDITOR_DEMO.md) shows cinematic draft authoring and quality-of-life features in Unity: beat selection, preview/scrubbing, navigation within a still, narration edits, and cue controls.

https://github.com/user-attachments/assets/fb88e504-3230-4687-aef0-3da6faf194cc

**Scope:** approximately five minutes, recorded October 5, 2026. The export pipeline and external PIL treatment are not shown. This is a related cinematic-authoring tool demonstration, not footage proving the regeneration/GUID-preservation behavior described below. [Full capture notes](../media/STORY_BEATS_EDITOR_DEMO.md).

## Problem

Authored animation strips do not always arrive as equal-width cells. Poses can touch, source canvases can differ, and a Hit animation can make a character appear larger than its Idle. Regeneration also risks breaking Unity references or overwriting a carefully edited playback sequence.

## Decision

The documented tool separates inspection, configuration, and output generation:

| Stage | Responsibility | Write boundary |
|---|---|---|
| Analyze / preflight | Inspect extraction candidates, shared canvas, normalization, and ownership | Read-only |
| Save profiles | Persist explicitly reviewed import configuration | Configuration assets |
| Generate / import | Generate from saved configuration | Outputs owned by those profiles |

A live window edit is not production configuration until saved. This prevents output from depending on an unrecorded UI state.

## Normalize at the import boundary

The manual describes an accepted Idle reference for character-scale suggestions, shared character canvases, and stable baselines. Correcting a mismatched Hit strip belongs to saved import configuration and generated frames.

Moving that correction to runtime transform scale would spread one source-art problem into presentation behavior and could introduce inconsistent transitions. Import-time correction makes the art usable across runtime consumers.

Shared-canvas expansion is a separate deliberate change: related profiles and outputs must be considered together. It should not become an incidental side effect of fixing one strip's apparent size.

## Output identity is part of the tool contract

Profiles record ownership of generated outputs. Ordinary regeneration updates existing owned assets in place. When output identities and Unity GUIDs remain stable, existing references continue pointing to the regenerated Sprites.

That guarantee is conditional. Introducing new variants, removing frames, or changing ownership can require an explicit migration. “Regenerate” is not permission to delete and recreate everything.

## Inventory and playback are different data

Folder Mode groups related key and in-between strips. Its generated-frame inventory answers which frames the tool owns. An editable playback sequence answers which frames should appear, in what order, with what repetitions or holds.

Regeneration preserves editorial playback when the inventory is unchanged. Removal is treated as a migration rather than silently rewriting the sequence. Playback speed belongs to runtime presentation configuration, so adding displayed frames requires a timing decision if the same duration is desired.

## Inspect the ownership and editorial boundary

The [animation-regeneration exhibit](../samples/ANIMATION_REGENERATION.md) contains selected production fragments for ownership validation, generated-inventory/playback synchronization, and a no-op preservation test. The test edits playback from generated `[A, B]` to `[B, A, A]` and asserts that regenerating the same inventory preserves it.

The displayed source was inspected, not compiled or tested for this showcase. It excludes extraction, normalization, complete generation, profile schemas, and artwork. This is evidence of a specific implementation decision rather than new verification of the complete pipeline.

## Additional pipeline demonstration plan

A strong tools demonstration would show a rights-cleared generic character with a visibly oversized Hit strip, then analysis, saved normalization, generation, and scale-consistent playback. A second segment would reorder a grouped sequence and regenerate to show editorial preservation.

The capture should include reference continuity and output ownership checks, not only attractive playback. The [media plan](MEDIA_AND_CODE_SAMPLE_PLAN.md) specifies the remaining evidence work.

## Tradeoff

Persistent profiles and owned outputs require configuration management and explicit migrations. They make repeated production work reviewable and reduce accidental reference churn. The documented acceptance recipe combines preflight, explicit saving, generation, identity/reference checks, relevant tests, and Play Mode visual review; that recipe is not a completed test report.
