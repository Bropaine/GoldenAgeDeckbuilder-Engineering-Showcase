# Engineering workflow

[Diagram index](README.md) · [AI-assisted engineering](../docs/AI_ASSISTED_ENGINEERING.md)

```mermaid
flowchart TD
    T["Authorized bounded task and baseline"] --> Q{"Player-facing behavior decided?"}
    Q -->|"No"| D["Compare options; record durable decision"]
    D --> Q
    Q -->|"Yes"| I["Inspect current files; implement bounded change"]
    I --> V["Verify identified state; record limits"]
    V --> R["Review pushed tip and actual diff"]
    R --> A["Exact-state acceptance"]
    A --> G["Integrate under safeguards; reconcile documentation"]
    classDef plain fill:#F3E7C6,stroke:#17130F,color:#17130F
    classDef work fill:#182A48,stroke:#17130F,color:#FFF7E6
    classDef gate fill:#D49A27,stroke:#17130F,color:#17130F
    class T,D,G plain
    class I,V,R work
    class Q,A gate
```

The workflow separates a product decision, a candidate implementation, verification, review, and acceptance. Any unresolved discrepancy pauses affected progression for reconciliation. This is a high-level policy diagram; it does not certify execution of a particular private ticket or publish its internal records.
