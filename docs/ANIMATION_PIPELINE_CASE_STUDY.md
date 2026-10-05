# Animation tooling: regeneration preserves production work

[Back to showcase](../README.md)

**Evidence scope:** this case study summarizes capabilities described by the supplied production animation manual. It does not assert a tooling acceptance date, test count, or fresh source verification.

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

## Demonstration plan

A strong tools demonstration would show a rights-cleared generic character with a visibly oversized Hit strip, then analysis, saved normalization, generation, and scale-consistent playback. A second segment would reorder a grouped sequence and regenerate to show editorial preservation.

The capture should include reference continuity and output ownership checks, not only attractive playback. The [media plan](MEDIA_AND_CODE_SAMPLE_PLAN.md) specifies the remaining evidence work.

## Tradeoff

Persistent profiles and owned outputs require configuration management and explicit migrations. They make repeated production work reviewable and reduce accidental reference churn. The documented acceptance recipe combines preflight, explicit saving, generation, identity/reference checks, relevant tests, and Play Mode visual review; that recipe is not a completed test report.
