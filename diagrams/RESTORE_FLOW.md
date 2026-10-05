# Restore flow

[Diagram index](README.md) · [Persistence case study](../docs/PERSISTENCE_CASE_STUDY.md)

```mermaid
flowchart TD
    S["Read saved candidate"] --> V{"Compatibility and semantic validation"}
    V -->|"Rejected"| F["Preserve live authority; report failure"]
    V -->|"Accepted candidate"| C["Construct separate state from saved facts"]
    C --> G{"Construction and graph valid?"}
    G -->|"No"| F
    G -->|"Yes"| A["Atomically install new live authority"]
    A --> B["Rebind presentation; invalidate stale requests"]
    classDef plain fill:#F3E7C6,stroke:#17130F,color:#17130F
    classDef runtime fill:#182A48,stroke:#17130F,color:#FFF7E6
    classDef gate fill:#D49A27,stroke:#17130F,color:#17130F
    class S,F,B plain
    class C,A runtime
    class V,G gate
```

Candidate work is separate from the live session. Failure replaces nothing. Successful installation preserves saved facts and establishes fresh live bindings. Normal initialization, reward decisions, shuffling, and settlement are not replayed during this path. The diagram describes live-state installation, not a disk-format or filesystem algorithm.
