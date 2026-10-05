# Combat command flow

[Diagram index](README.md) · [Combat case study](../docs/COMBAT_SYSTEM_CASE_STUDY.md)

```mermaid
flowchart TD
    I["Bound input request"] --> V{"Validate context, card, target, costs"}
    V -->|"Expected rejection"| F["Failure result; gameplay unchanged"]
    V -->|"Valid"| M["Authoritative gameplay resolution"]
    M --> R["Immutable completed result"]
    M --> S["Current runtime state"]
    R --> A["Presentation feedback"]
    S --> H["Presenter reads current totals"]
    classDef plain fill:#F3E7C6,stroke:#17130F,color:#17130F
    classDef rules fill:#182A48,stroke:#17130F,color:#FFF7E6
    classDef gate fill:#D49A27,stroke:#17130F,color:#17130F
    class I,F,R,A,H plain
    class M,S rules
    class V gate
```

Pending selection does not spend resources. Runtime validation governs the eventual command. Completed results describe applied actions, while current state supplies HUD totals. Feedback consumes the outcome without controlling gameplay completion. This diagram omits programming/configuration exception paths and detailed rule ordering.
