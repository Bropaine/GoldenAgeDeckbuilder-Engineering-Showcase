# State lifetimes

[Diagram index](README.md) · [Architecture](../docs/ARCHITECTURE.md)

```mermaid
flowchart TD
    D["Immutable authored definitions"] --> R["Persistent Run values and card copies"]
    D --> B["Fresh Battle objects"]
    R -->|"Snapshot current persistent inputs"| B
    B -->|"Validated completion settles once"| R
    R --> S["Durable snapshot"]
    B -->|"Supported durable combat facts"| S
    B --> P["Presenters and views"]
    classDef content fill:#F3E7C6,stroke:#17130F,color:#17130F
    classDef runtime fill:#182A48,stroke:#17130F,color:#FFF7E6
    classDef boundary fill:#D49A27,stroke:#17130F,color:#17130F
    class D,P content
    class R,B runtime
    class S boundary
```

Definitions may be shared. Mutable combat objects are recreated at fresh Battle boundaries. The Run owns persistent values and copies; a validated completion commits health once. Supported Run and combat facts can be captured together without retaining Unity views or old live object references.
