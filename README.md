# Golden Age Comics Roguelike Deckbuilder

### Engineering showcase · Unity · C# · Gameplay systems · Production tools

A turn-based roguelike deckbuilder centered on Black Terror, with a restored Golden Age comic presentation. This showcase explains the engineering behind its accepted vertical slice and subsequent card-identity and persistence milestones.

The central challenge is continuity: a Run must remember the exact cards the player owns and the decisions they have made, while every new Battle starts with fresh mutable combat objects. Saving and resuming must preserve that distinction—even in the middle of a supported combat decision.

**Production source is maintained privately.** This repository contains original public-facing architecture summaries, engineering case studies, conceptual diagrams, and a plan for selected media and code samples. This first edition contains documentation and diagrams; gameplay captures, executable samples, and a playable build are not included.

## At a glance

| Area | Documented project capability |
|---|---|
| Platform | Unity 6.5 / 6000.5.6f1, Universal Render Pipeline / Universal 2D, C# |
| State architecture | Immutable authored definitions, persistent Run state, fresh Battle state, focused rules, presenters and views |
| Combat | Ordered 1–3 enemy support, exact Battle-bound targeting, sequential enemy actions, group victory |
| Card ownership | Stable identity for each persistent card copy; exact-copy ordinary upgrades and removal rules |
| Continuation | Versioned authoritative snapshots, direct restore, stable-boundary autosave, explicit backup recovery |
| Verification | **Recorded September 23, 2026 checkpoint: 1,681 / 1,681 EditMode tests passing, zero failures**, plus packaged persistence acceptance |
| Tooling | A production manual describes animation-strip extraction, normalization, saved profiles, stable output ownership, and editable playback sequences |

These are documentation-backed checkpoint claims, not a new execution of the private project. Multi-enemy support was established primarily through proof fixtures; the documented four-encounter production route remained single-enemy content. The animation manual has a separate evidence basis from the September acceptance checkpoint. [Evidence and limits](docs/TESTING_AND_VERIFICATION.md).

## Start with these four case studies

| Case study | Engineering question | What to look for |
|---|---|---|
| [Combat](docs/COMBAT_SYSTEM_CASE_STUDY.md) | How do commands stay correct when targets and sessions change? | Exact identity, preflight validation, ordered resolution, result-driven feedback |
| [Persistence](docs/PERSISTENCE_CASE_STUDY.md) | How do you resume without accidentally playing the game again? | Direct reconstruction, candidate validation, atomic installation, durable RNG |
| [Persistent card identity](docs/CARD_IDENTITY_CASE_STUDY.md) | How do you upgrade one copy of a duplicated card? | Separate definition, Run-copy, and Battle-instance identities |
| [Animation tooling](docs/ANIMATION_PIPELINE_CASE_STUDY.md) | How do you regenerate art without breaking references or editorial work? | Saved configuration, owned outputs, stable asset identity, playback/inventory separation |

For a short architectural tour, read [Architecture](docs/ARCHITECTURE.md) and [Testing and verification](docs/TESTING_AND_VERIFICATION.md). For development practice, read [AI-assisted engineering](docs/AI_ASSISTED_ENGINEERING.md).

## Engineering responsibility and AI use

This is an Owner-led project developed with AI collaborators. The documented responsibilities span product decisions, state modeling, gameplay architecture, Unity integration, tooling, verification, and acceptance. The Owner retains material product and release decisions; AI supports technical analysis, implementation, review, and documentation within defined boundaries.

The case studies explain project decisions and their consequences. They do not imply that every production line was handwritten, that all development was autonomous, or that test counts prove individual authorship. [The methodology](docs/AI_ASSISTED_ENGINEERING.md) makes that division explicit.

## Development maturity

The Black Terror vertical slice was accepted on September 14, 2026. Individual card identity and deck refinement were accepted on September 21; durable continuation for the implemented capability set was accepted on September 23.

This is an in-development game. The showcased checkpoint does not establish a finished commercial release, broad content completeness, cloud saves, arbitrary future save migration, or keyboard/controller support. The accepted playtest record includes one independent fresh-player session; a second was deferred. Detailed limits are preserved in the verification document.

## Repository guide

```text
GoldenAgeDeckbuilder-Engineering-Showcase/
├── README.md
├── NOTICE.md
├── .gitignore
├── docs/
│   ├── PROJECT_OVERVIEW.md
│   ├── ARCHITECTURE.md
│   ├── COMBAT_SYSTEM_CASE_STUDY.md
│   ├── PERSISTENCE_CASE_STUDY.md
│   ├── CARD_IDENTITY_CASE_STUDY.md
│   ├── ANIMATION_PIPELINE_CASE_STUDY.md
│   ├── UI_ARCHITECTURE.md
│   ├── TESTING_AND_VERIFICATION.md
│   ├── AI_ASSISTED_ENGINEERING.md
│   ├── PUBLIC_RELEASE_BOUNDARIES.md
│   └── MEDIA_AND_CODE_SAMPLE_PLAN.md
├── diagrams/
│   ├── README.md
│   ├── STATE_LIFETIMES.md
│   ├── COMBAT_COMMAND_FLOW.md
│   ├── RESTORE_FLOW.md
│   └── ENGINEERING_WORKFLOW.md
├── media/
│   └── README.md
└── samples/
    └── README.md
```

[Project overview](docs/PROJECT_OVERVIEW.md) · [UI architecture](docs/UI_ARCHITECTURE.md) · [All diagrams](diagrams/README.md) · [Media and sample plan](docs/MEDIA_AND_CODE_SAMPLE_PLAN.md) · [Public-release boundaries](docs/PUBLIC_RELEASE_BOUNDARIES.md)

*Edition prepared October 5, 2026. Gameplay claims are bounded to the documented September 23 checkpoint unless explicitly identified otherwise. Rights and redistribution terms are described in [NOTICE](NOTICE.md).*
