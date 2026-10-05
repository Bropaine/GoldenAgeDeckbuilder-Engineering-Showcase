# Assembly boundaries: enforce dependency direction

[Sample index](README.md) · [Architecture](../docs/ARCHITECTURE.md)

**Selected production Unity assembly definitions, inspected October 5, 2026.** These two small configurations are included as architecture evidence. They are not a complete Unity project or a new build result.

## Definitions remain independent of engine state

The card-definition assembly declares no assembly dependencies and sets `noEngineReferences` to true.

```json
﻿{
  "name": "Cards.Definitions",
  "rootNamespace": "Cards.Definitions",
  "references": [],
  "includePlatforms": [],
  "excludePlatforms": [],
  "allowUnsafeCode": false,
  "overrideReferences": false,
  "precompiledReferences": [],
  "autoReferenced": true,
  "defineConstraints": [],
  "versionDefines": [],
  "noEngineReferences": true
}
```

*Production origin: Cards.Definitions.asmdef, lines 1–14.*

## Runtime depends on definitions

The combat-runtime assembly references card, encounter, and Run definitions. It excludes engine references and does not reference presentation.

```json
﻿{
  "name": "Combat.Runtime",
  "rootNamespace": "Combat.Runtime",
  "references": [
    "Cards.Definitions",
    "Encounters.Definitions",
    "Runs.Definitions"
  ],
  "includePlatforms": [],
  "excludePlatforms": [],
  "allowUnsafeCode": false,
  "overrideReferences": false,
  "precompiledReferences": [],
  "autoReferenced": true,
  "defineConstraints": [],
  "versionDefines": [],
  "noEngineReferences": true
}
```

*Production origin: Combat.Runtime.asmdef, lines 1–18.*

## What this demonstrates

Responsibility diagrams can drift unless the build structure enforces their intended direction. These configurations make it possible to keep runtime rules independent of Unity scene components while authoring and views remain Unity concerns.

The configuration is bounded evidence: it shows declared dependencies at the inspected source state. It does not independently certify all code quality, validate every transitive dependency, or report a successful compilation.

The actual `Combat.Presentation` assembly is Unity-enabled and references TextMeshPro. Individual plain-C# presenters should not be described as proof that the whole presentation assembly is engine-independent.

[Target identity](CONTEXT_BOUND_TARGETS.md) · [Presentation boundary](PRESENTATION_WITHOUT_MUTATION.md) · [Redistribution notice](../NOTICE.md)

