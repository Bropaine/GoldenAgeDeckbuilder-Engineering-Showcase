# Selected production code

[Back to showcase](../README.md) · [Media and sample plan](../docs/MEDIA_AND_CODE_SAMPLE_PLAN.md)

This collection contains **reviewed production excerpts**, selected for their small scope and clear architectural purpose. Source and selected test methods were inspected on October 5, 2026. They were not compiled or executed for this showcase.

| Exhibit | What to inspect | Included material |
|---|---|---|
| [Context-bound targets](CONTEXT_BOUND_TARGETS.md) | Identity includes the owning Battle; a foreign target is rejected even when ID values match | Equality and resolution fragments plus one test method |
| [Presentation without mutation](PRESENTATION_WITHOUT_MUTATION.md) | View updates do not own gameplay health | Small view interface, presenter method, and one test method |
| [Assembly boundaries](ASSEMBLY_BOUNDARIES.md) | Dependencies are declared and engine references are restricted | Two small Unity assembly definitions |
| [Durable save commit](DURABLE_SAVE_COMMIT.md) | Persisted bytes are validated before commit; failures retain dirty state | Storage-port and save-service fragments, plus failure assertions |
| [Staged session restore](STAGED_SESSION_RESTORE.md) | Rejection preserves live authority; installation follows candidate construction | Restore and installation fragments, plus unchanged-state assertions |

Each exhibit explains its scope, source filename and reviewed line range, omissions, and evidence limits. The excerpts retain their production names and behavior. They are not newly authored representative examples.

## Scope and reuse

These Markdown exhibits are reading artifacts, not a buildable project. Their containing types, helper methods, referenced assemblies, and wider runtime are intentionally omitted. The complete combat path, game content, save formats, asset pipeline, and private repository history remain private.

Selected test methods and assertion fragments show how a property is expressed in the private suite. Reading a test does not establish that it passes, and historical suite totals do not certify this later source inspection. See [Testing and verification](../docs/TESTING_AND_VERIFICATION.md).

Public availability does not grant an open-source or redistribution license. See [NOTICE](../NOTICE.md). Future standalone representative examples should identify themselves separately, include actual execution evidence, and carry explicit terms after Owner selection.
