# Animation regeneration: preserve asset identity and editorial intent

[Sample index](README.md) · [Animation tooling case study](../docs/ANIMATION_PIPELINE_CASE_STUDY.md)

**Selected production Unity C# excerpts, inspected October 5, 2026.** These fragments use Unity Editor and Sprite APIs. They are non-standalone reading artifacts; the test source was inspected, not executed for this showcase.

## Decision: generation owns inventory, the artist owns playback

An animation tool must handle more than producing images. Regenerating a frame can break scene references if its asset identity changes. Rebuilding a sequence from extraction order can discard an artist's timing and ordering choices.

The tool gives those concerns separate owners:

| Concern | Authority |
|---|---|
| Which output paths and asset identities may be regenerated | Saved ownership records |
| Which generated frames exist | Generated inventory |
| Which frames play, in what order, with repetitions | Editorial playback sequence |

This distinction is useful in asset pipelines, document generators, and tools that combine machine output with human edits.

### Refuse an overwrite without ownership

On first generation, an existing file, metadata file, or loaded Unity asset blocks the output path. On regeneration, the saved record must match the frame index, path, and current Unity GUID. An altered count requires explicit migration.

```csharp
    private static void ValidateOwnership(Input x, int index, string path)
    {
        var ledger = x.OwnedFrames;
        if (ledger == null || ledger.Count == 0)
        {
            if (File.Exists(path) || File.Exists(path + ".meta") || AssetDatabase.LoadMainAssetAtPath(path) != null)
                throw new IOException("Unowned output path exists: " + path);
            return;
        }
        if (ledger.Count != x.Count) throw new InvalidDataException("Owned frame count changed; explicit migration required: " + path);
        var record = ledger.FirstOrDefault(f => f.Index == index);
        if (record == null || record.Path != path || string.IsNullOrEmpty(record.AssetGuid) ||
            !File.Exists(path) || AssetDatabase.AssetPathToGUID(path) != record.AssetGuid)
            throw new IOException("Owned output missing, moved or GUID-changed: " + path);
    }
```

*Production origin: GoldenAgeAnimationPipeline.cs, lines 300–314. Input, ownership records, and generation callers are omitted.*

This is a preflight check, not an authorization mechanism against another process modifying files. It constrains the editor tool's intended writes.

### Preserve editorial order instead of rebuilding it

The character-animation synchronization method checks the current editorial sequence before its no-op return. It rejects null or foreign frames even when the generated inventory is unchanged.

If the inventory changes, removing an old frame requires migration. Existing playback order and repetitions are copied; genuinely new frames are appended. First population adopts generated order.

```csharp
            var old = generatedFrames.ToList();
            if (old.Count != 0 && playbackFrames.Count == 0)
                throw new InvalidOperationException("Editorial sequence is empty.");
            if (playbackFrames.Any(x => x == null || !old.Contains(x)))
                throw new InvalidOperationException("Editorial sequence refers to a null or foreign frame.");
            if (generatedFrames.SequenceEqual(frames)) return false;
            if (old.Any(x => !frames.Contains(x)))
                throw new InvalidOperationException("Removing generated frames requires an explicit editorial migration.");
            var nextPlayback = playbackFrames.ToList();
            foreach (Sprite frame in frames)
                if (!old.Contains(frame)) nextPlayback.Add(frame);
            if (old.Count == 0) nextPlayback = frames.ToList();
            characterKey = character;
            stateKey = state;
            sourceFolderGuid = folderGuid;
            generatedFrames = frames.ToList();
            playbackFrames = nextPlayback;
            return true;
```

*Production origin: GroupedAnimationSetAsset.cs, lines 40–57. Excerpt from EditorSync; required-argument and source/identity guards precede this fragment.*

The generated inventory can be `[A, B]` while playback is `[B, A, A]`. Repeating A encodes an editorial hold. Regenerating the same inventory should preserve that choice.

### Express the preservation property in a test

The omitted fixture creates a grouped set and two Sprites, `a` and `b`. It then edits the serialized playback sequence to `[b, a, a]`, requests the same generated inventory, and asserts a no-op with the sequence intact.

```csharp
            Assert.IsTrue(set.EditorSync("black-terror", "idle", "folder-guid", new[] { a, b }));
            var serialized = new SerializedObject(set);
            var order = serialized.FindProperty("playbackFrames");
            order.arraySize = 3;
            order.GetArrayElementAtIndex(0).objectReferenceValue = b;
            order.GetArrayElementAtIndex(1).objectReferenceValue = a;
            order.GetArrayElementAtIndex(2).objectReferenceValue = a;
            serialized.ApplyModifiedPropertiesWithoutUndo();
            Assert.IsFalse(set.EditorSync("black-terror", "idle", "folder-guid", new[] { a, b }));
            CollectionAssert.AreEqual(new[] { b, a, a }, set.PlaybackFrames.ToArray());
```

*Production origin: GroupedAnimationSetAssetTests.cs, lines 18–27. Excerpt from NoOpRegenerationPreservesEditorialHoldsAndOrder; NUnit attribute, object creation, and cleanup are omitted.*

The assertion describes a specific regression property. It is not a newly reported passing result.

## Why it belongs in an engineering portfolio

The interesting decision is the boundary between repeatable generation and human authorship. Re-running the tool is deliberately insufficient authority to overwrite unrelated assets, change established identities, or discard editorial choices.

That exposes a production concern a simple importer can miss: correctness includes preserving work already done by other parts of the workflow.

## Related editor demonstration

The [Story Beats V2 recording](../media/STORY_BEATS_EDITOR_DEMO.md) shows cinematic authoring and quality-of-life features in a related Unity tool. It is useful visual context for production tooling, but does not exercise the ownership or regeneration assertions in this exhibit. Export and external PIL treatment are outside that recording.

## Tradeoffs and scope

Saved ownership records require maintenance. Missing, moved, or GUID-changed outputs stop ordinary regeneration; intentional structural changes need an explicit migration.

Appending new frames is a policy, not automatic timing preservation. More playback frames can change duration unless runtime timing is adjusted. The no-op test does not demonstrate every inventory-change or write-failure case.

These snippets show ownership and sequence policy. They do not establish successful compilation, filesystem transactionality, cross-process safety, or recovery from arbitrary disk failure. The full generator, profile schema, extraction and normalization algorithms, artwork, and cinematic branch remain private.

[Evidence scope](../docs/TESTING_AND_VERIFICATION.md) · [Redistribution notice](../NOTICE.md)
