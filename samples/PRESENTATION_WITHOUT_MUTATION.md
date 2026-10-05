# Presentation without mutation: reveal an outcome without applying it again

[Sample index](README.md) · [UI architecture](../docs/UI_ARCHITECTURE.md)

**Selected production C# excerpts, reviewed October 5, 2026.** These fragments omit containing types, dependency injection setup, fake views, and fixture helpers. They are not standalone executable samples. The test source was inspected, not executed for this publication.

## Decision

Combat resolves authoritative health before presentation reveals its feedback. A presenter can display a previously resolved health value while live Battle state and committed Run state remain unchanged.

### A narrow view contract

The interface accepts values. It does not expose damage, settlement, progression, or a mutable Battle.

```csharp
    public interface IBattlePlayerHealthView
    {
        void SetPlayerHealth(
            int currentHitPoints,
            int maximumHitPoints);
    }
```

*Production origin: IBattlePlayerHealthView.cs, lines 3–8.*

### Validate presentation values and forward them

`BattlePlayerHealthPresenter` receives its view through constructor injection. Its `PresentValues` method validates the display input and forwards it to that view.

```csharp
        public void PresentValues(
            int currentHitPoints,
            int maximumHitPoints)
        {
            if (maximumHitPoints <= 0)
            {
                throw new ArgumentOutOfRangeException(
                    nameof(maximumHitPoints),
                    "Maximum Hit Points must be greater than zero.");
            }

            if (currentHitPoints < 0 ||
                currentHitPoints > maximumHitPoints)
            {
                throw new ArgumentOutOfRangeException(
                    nameof(currentHitPoints),
                    "Current Hit Points must be within the valid range.");
            }

            _view.SetPlayerHealth(
                currentHitPoints,
                maximumHitPoints);
        }
```

*Production origin: BattlePlayerHealthPresenter.cs, lines 39–61. Constructor and state-reading Present method are omitted.*

### Keep displayed and authoritative values distinct

The fixture begins with committed Run health of 34 and live Battle health of 10. Displaying 7 updates the fake view while the two authoritative values stay unchanged.

```csharp
        public void BattleHealthPresenter_PresentValues_UpdatesViewWithoutMutatingBattle()
        {
            ActiveCarryoverScenario scenario =
                CreateActiveCarryoverScenario(
                    committedHitPoints: 34,
                    liveHitPoints: 10);

            var view =
                new FakeBattleHealthView();

            var presenter =
                new BattlePlayerHealthPresenter(
                    view);

            presenter.PresentValues(
                currentHitPoints: 7,
                maximumHitPoints: 50);

            Assert.That(
                view.CurrentHitPoints,
                Is.EqualTo(7));

            Assert.That(
                view.MaximumHitPoints,
                Is.EqualTo(50));

            Assert.That(
                scenario.Battle.Player.CurrentHitPoints,
                Is.EqualTo(10));

            Assert.That(
                scenario.Run.CurrentHitPoints,
                Is.EqualTo(34));
        }
```

*Production origin: AuthoritativeRunHealthPresenterTests.cs, lines 207–240. The NUnit attribute and fixture implementation are omitted.*

## Why the boundary matters

If a delayed animation applies damage, presentation timing can decide gameplay correctness. A value-only presenter supports a visual reveal after resolution without reapplying the hit. The separate Run-health presenter reads committed health between Battles; composition chooses which lifetime to display.

## Tradeoff and scope

This is a focused presenter boundary. It does not prove that every callback or view in the game is mutation-free. The current presentation assembly permits Unity references even though this small presenter uses plain C#; the runtime assembly excludes engine references.

An invalid display value is a programming/configuration error and throws before the view call. Gameplay command rejection is a different concern and uses runtime validation/results.

[Assembly evidence](ASSEMBLY_BOUNDARIES.md) · [Evidence scope](../docs/TESTING_AND_VERIFICATION.md) · [Redistribution notice](../NOTICE.md)

