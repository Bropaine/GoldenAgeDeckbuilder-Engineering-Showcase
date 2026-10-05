# Context-bound targets: the owner is part of identity

[Sample index](README.md) · [Combat case study](../docs/COMBAT_SYSTEM_CASE_STUDY.md)

**Selected production C# excerpts, reviewed October 5, 2026.** Containing types, model dependencies, and test-fixture helpers are omitted. These fragments are not standalone executable samples. Their source was inspected; the tests were not run for this publication.

## Decision

Enemy identifiers are local to a Battle. A replacement Battle can contain an equal identifier without containing the same live target. The target therefore captures both the exact Battle context and the enemy identifier.

### Ownership and equality

In `BattleEnemyTarget`, `_battle` is the captured Battle reference. Membership uses reference identity; equality requires both that context and the enemy ID.

```csharp
        public bool BelongsTo(
            BattleState battle)
        {
            return battle != null &&
                   ReferenceEquals(
                       _battle,
                       battle);
        }

        public bool Equals(
            BattleEnemyTarget other)
        {
            return ReferenceEquals(
                       _battle,
                       other._battle) &&
                   EnemyInstanceId.Equals(
                       other.EnemyInstanceId);
        }
```

*Production origin: BattleEnemyTarget.cs, lines 48–65.*

### Reject a foreign context before resolving membership

The resolver initializes its output to null, then rejects an invalid or foreign target before searching the authoritative enemy collection.

```csharp
            enemy =
                null;

            if (!target.IsValid ||
                !target.BelongsTo(
                    battle))
            {
                return false;
            }
```

*Production origin: BattleEnemyTargetResolver.cs, lines 25–33; excerpt from TryResolveLivingEnemy.*

### Test the dangerous case

The fixture helper creates two different Battles with equal local enemy IDs. The test submits a target from the first Battle to the second and asserts rejection.

```csharp
        public void Resolve_ForeignBattleTarget_FailsEvenWhenIdValuesMatch()
        {
            BattleState firstBattle =
                CreateBattle(
                    20,
                    30);

            BattleState secondBattle =
                CreateBattle(
                    20,
                    30);

            Assert.That(
                firstBattle.Enemies[0]
                    .InstanceId,
                Is.EqualTo(
                    secondBattle.Enemies[0]
                        .InstanceId));

            var staleTarget =
                new BattleEnemyTarget(
                    firstBattle,
                    firstBattle.Enemies[0]
                        .InstanceId);

            bool resolved =
                new BattleEnemyTargetResolver()
                    .TryResolveLivingEnemy(
                        secondBattle,
                        staleTarget,
                        out EnemyRuntimeEntry enemy);

            Assert.That(
                resolved,
                Is.False);

            Assert.That(
                enemy,
                Is.Null);
        }
```

*Production origin: BattleEnemyTargetResolverTests.cs, lines 102–141. The NUnit test attribute and CreateBattle helper are outside this fragment.*

## Why the boundary matters

A list position or raw ID cannot authorize a request after the owning session changes. Binding identity to its lifetime makes stale input rejectable in the rules layer even if the UI has not yet cleared its old selection.

The same pattern is useful for document sessions, editor tools, and other applications where local identifiers can repeat across replacement contexts.

## Tradeoff and scope

The production implementation relies on exact in-memory object identity. A distributed or networked system would need a different context token. This excerpt does not establish network identity, globally unique enemy IDs, or complete card-play atomicity.

Expected foreign-target rejection returns false. Other omitted validation covers living membership; card costs and effects belong to separate services. The private Battle model, content, combat arithmetic, and full command path are not included.

[Evidence scope](../docs/TESTING_AND_VERIFICATION.md) · [Redistribution notice](../NOTICE.md)

