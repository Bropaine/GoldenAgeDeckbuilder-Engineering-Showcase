# Staged restore: construct before replacing live authority

[Sample index](README.md) · [Persistence case study](../docs/PERSISTENCE_CASE_STUDY.md) · [Durable save](DURABLE_SAVE_COMMIT.md)

**Selected production C# excerpts, inspected October 5, 2026.** Snapshot schemas, validators, reconstruction internals, containing types, and test-fixture helpers remain private. These fragments are not standalone executable samples. The displayed test source was inspected, not run for this showcase.

## Decision: restore saved facts without replaying gameplay

Loading a Run can replace several owners at once: persistent Run state, an active Battle, and gameplay RNG state. Installing a partly reconstructed candidate would leave those owners inconsistent. The service therefore validates and constructs the replacement graph before crossing its installation boundary.

### Reject invalid input before replacement

After required-argument checks, semantic validation returns an ordinary failure result for an invalid snapshot.

```csharp
            RunSessionSnapshotValidationResult validation =
                _validator.Validate(
                    snapshot);

            if (!validation.IsValid)
            {
                return RunSessionRestoreResult.Failure(
                    validation);
            }
```

*Production origin: RunSessionRestoreService.cs, lines 77–85. Excerpt from TryRestore.*

The omitted section constructs separate Run and Battle candidates. A reconstruction rejection after composite validation is treated as a broken invariant and throws before installation. Normal New Run initialization, shuffle, draw, reward generation, and settlement are not the reconstruction path.

### Finish fallible preparation before installation

The RNG candidate is reconstructed separately. The next binding generation is computed before replacement because its checked increment can overflow. Fresh bindings invalidate obsolete live requests even when durable identifiers repeat.

```csharp
            GameplayRngSession restoredRng =
                GameplayRngSession.Restore(
                    snapshot.GameplayRng);

            /*
             * This can throw on generation overflow, so calculate it
             * before any live authority is replaced.
             */
            LiveBindingGeneration newBindingGeneration =
                runSession.CreateNextBindingGeneration();

            RunState restoredRun =
                runResult.Candidate.Run;

            /*
             * Commit boundary.
             *
             * All validation and candidate construction that can fail
             * has completed above. These internal installation methods
             * are deliberately assignment/state-copy only and execute no
             * gameplay rules.
             */
            runSession.InstallRestored(
                restoredRun,
                runResult.Candidate.Definition,
                runResult.Candidate.AttemptId,
                newBindingGeneration);

            battleSession.InstallRestored(
                restoredRun,
                battleResult.Battle);

            gameplayRngSession.InstallRestoredState(
                restoredRng);

            return RunSessionRestoreResult.Success(
                newBindingGeneration);
```

*Production origin: RunSessionRestoreService.cs, lines 115–151. Run and Battle candidate reconstruction immediately preceding this fragment is omitted.*

### Keep the trusted installation seam narrow

The Run installation method assigns already-prepared values. It does not initialize gameplay, generate a reward, settle health, or draw a card.

```csharp
        internal void InstallRestored(
            RunState run,
            RunDefinition definition,
            RunAttemptId attemptId,
            LiveBindingGeneration bindingGeneration)
        {
            _currentRunDefinition =
                definition;

            _currentRun =
                run;

            _currentAttemptId =
                attemptId;

            _currentBindingGeneration =
                bindingGeneration;
        }
```

*Production origin: RunSession.cs, lines 183–200. Other session methods are omitted.*

The inspected Battle installation collaborator also assigns the staged owners. RNG installation copies state into existing stream objects, preserving references held by injected shufflers and reward samplers. The RNG algorithm and full installation collaborators are not included.

### Check rejection preserves every live owner

In `TryRestore_InvalidCandidatePreservesEntireActiveSession`, the fixture captures the original live Run, Battle, attempt, binding generation, RNG state, and shuffle count. It then submits an invalid candidate and asserts failure. This excerpt shows the unchanged-state assertions.

```csharp
            Assert.That(
                live.RunSession.CurrentRun,
                Is.SameAs(
                    oldRun));

            Assert.That(
                live.BattleSession.CurrentBattle,
                Is.SameAs(
                    oldBattle));

            Assert.That(
                live.RunSession.CurrentAttemptId,
                Is.EqualTo(
                    oldAttempt));

            Assert.That(
                live.RunSession.CurrentBindingGeneration,
                Is.EqualTo(
                    oldGeneration));

            AssertGameplayRngStateEqual(
                rngBefore,
                live.GameplayRng.CaptureState());

            Assert.That(
                live.OpeningShuffler.CallCount,
                Is.EqualTo(
                    shuffleCallsBefore));
```

*Production origin: P03AtomicRunSessionRestoreTests.cs, lines 199–226. Assertion fragment; fixture construction, invalid candidate, restore invocation, and failure assertions are omitted.*

## Why this boundary matters

Saved facts and fresh gameplay initialization serve different purposes. Reusing initialization can consume randomness or apply a decision twice. Separate reconstruction and installation make the transition inspectable and let rejection preserve the current session.

The explicit binding generation connects persistence to the [context-bound targeting decision](CONTEXT_BOUND_TARGETS.md): durable identity can survive a restore while old live references lose authority.

## Tradeoffs and limits

This is synchronous logical installation under validated invariants and trusted assignment/state-copy methods. It is not a single hardware-atomic write or a concurrent multi-threaded transaction. A future installation callback that performs fallible work would undermine the boundary and require redesign or additional staging.

The displayed rejection test is source evidence, not a new passing result. It does not exercise disk crash recovery. Snapshot layouts, complete reconstruction, compatibility internals, game content, and RNG algorithm internals remain private.

[Evidence scope](../docs/TESTING_AND_VERIFICATION.md) · [Redistribution notice](../NOTICE.md)
