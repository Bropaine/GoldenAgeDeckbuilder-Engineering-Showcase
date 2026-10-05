# Durable save: validate persisted bytes before commit

[Sample index](README.md) · [Persistence case study](../docs/PERSISTENCE_CASE_STUDY.md) · [Staged restore](STAGED_SESSION_RESTORE.md)

**Selected production C# excerpts, inspected October 5, 2026.** These are non-standalone reading artifacts. Containing types, snapshot formats, codec internals, test doubles, and fixture helpers are omitted. The displayed test source was inspected, not executed for this showcase.

## Decision: a successful serialization is not a successful save

The save service depends on separate codec, filesystem, semantic-validator, and durability-state collaborators. It serializes first, writes a temporary file, then reads that file back. Decoding and semantic validation operate on the bytes actually read from storage, rather than assuming the original in-memory candidate is what persisted.

The logical order is:

```text
serialize → write temporary file → read it back → decode → validate
          → commit temporary file as primary → mark clean → report success
```

### A focused storage port

The filesystem interface makes writes and commits injectable. Tests can fail an operation without needing a real failing disk.

```csharp
        void WriteAllBytesDurably(
            string path,
            byte[] bytes);

        void CommitTempToPrimary(
            string tempPath,
            string primaryPath,
            string backupPath);
```

*Production origin: IRunSaveFileSystem.cs, lines 17–24. Excerpt from the interface; read, existence, and deletion members are omitted.*

### Validate the read-back candidate

The earlier temporary write/read and their error handling are omitted here. At this point, `persistedTempBytes` came from the filesystem read. Decode or semantic rejection returns before the commit path and retains dirty state.

```csharp
            RunSessionSnapshotDecodeResult decodeResult =
                _codec.TryDeserialize(
                    persistedTempBytes);

            if (!decodeResult.Succeeded)
            {
                _durabilityState.MarkDirty();

                return RunSessionSaveResult.Failure(
                    RunSessionSaveFailureReason
                        .TempDecodeFailed,
                    decodeResult.Message,
                    decodeResult);
            }

            RunSessionSnapshotValidationResult validationResult =
                _validator.Validate(
                    decodeResult.Snapshot);

            if (!validationResult.IsValid)
            {
                _durabilityState.MarkDirty();

                return RunSessionSaveResult.Failure(
                    RunSessionSaveFailureReason
                        .TempValidationFailed,
                    validationResult.Message,
                    decodeResult,
                    validationResult);
            }
```

*Production origin: RunSessionSaveService.cs, lines 149–178. Excerpt from TrySave.*

### Report success only after commit

The omitted commit block chooses normal primary-to-backup rotation or recovery with the existing backup preserved. This catch handles the service's defined storage-failure categories. A failed commit returns a failure result; clean state and success appear after that block.

```csharp
            catch (Exception exception)
                when (IsStorageFailure(
                          exception))
            {
                _durabilityState.MarkDirty();

                return RunSessionSaveResult.Failure(
                    RunSessionSaveFailureReason
                        .CommitFailed,
                    exception.Message,
                    decodeResult,
                    validationResult);
            }

            _durabilityState.MarkClean();

            return RunSessionSaveResult.Success(
                decodeResult,
                validationResult);
```

*Production origin: RunSessionSaveService.cs, lines 221–239. The preceding commit-mode switch and exception classifier are omitted.*

### Assert failure preserves the previous save

In `TrySave_TruncatedTempReadback_DoesNotCommitAndRemainsDirty`, a scripted filesystem starts with an existing primary and returns truncated bytes when the temporary file is read. The omitted setup invokes the actual save service and asserts a decode failure. These final assertions check dirty state, zero commits, and unchanged primary bytes.

```csharp
            Assert.That(
                durability.IsDirty,
                Is.True);

            Assert.That(
                fileSystem.CommitCount,
                Is.Zero);

            CollectionAssert.AreEqual(
                previousPrimary,
                fileSystem.ReadStoredBytes(
                    paths.PrimaryPath));
```

*Production origin: P03RunSessionSaveServiceTests.cs, lines 269–280. Assertion fragment; method declaration, failure setup, candidate helper, and scripted filesystem are omitted.*

## Why this is useful architecture evidence

Serialization, storage, semantic correctness, and player-facing save cadence have separate responsibilities. The save service owns the write/validate/commit sequence. Higher-level orchestration decides when a stable gameplay boundary should be saved.

The inspected desktop adapter writes with `FileStream.Flush(flushToDisk: true)`. On normal replacement of an existing primary it uses `File.Replace` with the previous primary as backup; first-generation creation uses `File.Move`. Recovery has a separate operation that preserves the known backup. These are descriptions of inspected implementation, not new filesystem execution evidence.

## Tradeoffs and limits

Temporary read-back adds I/O and validation work. It provides a concrete rejection boundary for a bad temporary candidate; it does not prove all devices or filesystems survive every power-loss scenario.

Normal rotation deletes an older backup before replacement, so a failed replacement can leave the current primary intact without retaining that older backup. The scripted test above exercises corrupt read-back before commit, not the physical adapter's crash behavior.

The fragments do not reveal the binary format, real save paths, schema fields, canonical content manifest, compatibility matrix, or complete storage adapter. They also do not prove protection from total device loss or guarantee migration for future mechanics.

[Evidence scope](../docs/TESTING_AND_VERIFICATION.md) · [Redistribution notice](../NOTICE.md)
