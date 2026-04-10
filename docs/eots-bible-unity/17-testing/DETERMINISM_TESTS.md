# Determinism Tests

## Why Determinism is Critical

Rollback netcode requires bit-identical simulation across all clients and the server.
Any non-determinism causes desyncs, broken gameplay, and trust violations.

## Determinism Test Matrix

| Test | Scope | Ticks | Frequency | Platform |
|------|-------|-------|-----------|----------|
| Basic 2-world | Full simulation | 1,000 | Every CI | Native |
| Extended 2-world | Full simulation | 10,000 | Nightly | Native |
| 4-player simulation | 4 worlds, shared inputs | 1,000 | Every CI | Native |
| Cross-platform | C# vs C# (Win/Linux/Mac) | 1,000 | Nightly | Cross-compile |
| Rust FFI | C# RNG vs Rust RNG | 10,000 values | Every CI | Native + FFI |
| Rollback determinism | Rollback + resim = same state | 1,000 | Every CI | Native |
| Serialization round-trip | Serialize → deserialize → compare | 500 entities | Every CI | Native |

## Core Test Implementation

```csharp
[Test]
public void TwoWorlds_IdenticalInputs_IdenticalState_1000Ticks()
{
    var seed = 42UL;
    var world1 = new EotSWorld(seed);
    var world2 = new EotSWorld(seed);
    
    var rng = new Xoshiro256StarStar(seed);
    
    for (int tick = 0; tick < 1000; tick++)
    {
        // Generate random but identical inputs for both worlds
        var inputs = GenerateRandomInputs(rng, playerCount: 4);
        
        world1.SetInputs(inputs);
        world2.SetInputs(inputs);
        
        world1.Tick();
        world2.Tick();
        
        // Check every 10 ticks
        if (tick % 10 == 0)
        {
            var hash1 = world1.ComputeChecksum();
            var hash2 = world2.ComputeChecksum();
            Assert.AreEqual(hash1, hash2, 
                $"Desync at tick {tick}: world1={hash1:X8} world2={hash2:X8}");
        }
    }
    
    // Final full comparison
    var bytes1 = world1.SerializeToBytes();
    var bytes2 = world2.SerializeToBytes();
    EotSAssert.NoByteDiff(bytes1, bytes2, "Final state");
}
```

## Rollback Determinism Test

```csharp
[Test]
public void Rollback_ProducesSameState_AsLinearSimulation()
{
    var seed = 123UL;
    var worldLinear = new EotSWorld(seed);
    var worldRollback = new EotSWorld(seed);
    
    var allInputs = PreGenerateInputs(seed, ticks: 100, players: 4);
    
    // Linear: simulate straight through
    for (int t = 0; t < 100; t++)
    {
        worldLinear.SetInputs(allInputs[t]);
        worldLinear.Tick();
    }
    
    // Rollback: simulate to tick 50, save snapshot, continue to 80,
    // then rollback to 50 and resim with same inputs
    for (int t = 0; t < 50; t++)
    {
        worldRollback.SetInputs(allInputs[t]);
        worldRollback.Tick();
    }
    var snapshot = worldRollback.SaveSnapshot();
    
    for (int t = 50; t < 80; t++)
    {
        worldRollback.SetInputs(allInputs[t]);
        worldRollback.Tick();
    }
    
    // Rollback to tick 50
    worldRollback.RestoreSnapshot(snapshot);
    
    // Resimulate 50-100
    for (int t = 50; t < 100; t++)
    {
        worldRollback.SetInputs(allInputs[t]);
        worldRollback.Tick();
    }
    
    Assert.AreEqual(
        worldLinear.ComputeChecksum(),
        worldRollback.ComputeChecksum(),
        "Rollback must produce identical state to linear simulation");
}
```

## Determinism Violation Checklist

Sources of non-determinism and their mitigations:

| Source | Risk | Mitigation | Test |
|--------|------|-----------|------|
| `float` / `double` arithmetic | High | FixedPoint64 only in Core/ | Grep for float in Core/ |
| `Dictionary` iteration order | High | Use sorted collections or arrays | Review all iterations |
| `DateTime.Now` | Medium | Use tick counter only | Grep for DateTime in Core/ |
| `System.Random` | High | xoshiro256** only | Grep for System.Random |
| `Guid.NewGuid()` | Medium | Sequential entity IDs | Grep for Guid in Core/ |
| `GetHashCode()` | Medium | Custom hash functions | Review hash usage |
| `Task` / `async` | High | Synchronous simulation only | Grep for async in Core/ |
| `Parallel.For` | High | Single-threaded sim | Grep for Parallel in Core/ |
| Uninitialized memory | Medium | Zero-init all components | Constructor review |
| Platform endianness | Low | Little-endian assumed | Cross-platform test |
| IL2CPP vs Mono | Medium | Test both backends | CI matrix |

## Static Analysis Gates

Run before tests to catch determinism violations at compile time:

| Rule | Check | Action |
|------|-------|--------|
| No float in Core/ | Roslyn analyzer | Error |
| No System.Random in Core/ | Roslyn analyzer | Error |
| No DateTime in Core/ | Roslyn analyzer | Error |
| No async in Core/ | Roslyn analyzer | Error |
| No Dictionary without IComparer | Roslyn analyzer | Warning |
| No GetHashCode() in simulation | Roslyn analyzer | Warning |

## Cross-Platform Test Procedure

```yaml
# .github/workflows/determinism-crossplat.yml
jobs:
  generate-reference:
    runs-on: ubuntu-latest
    steps:
      - run: dotnet test --filter "Determinism" --logger "trx"
      - run: cp TestResults/checksums.bin reference_checksums.bin
      - uses: actions/upload-artifact@v4
        with: { name: reference, path: reference_checksums.bin }

  verify-windows:
    needs: generate-reference
    runs-on: windows-latest
    steps:
      - uses: actions/download-artifact@v4
        with: { name: reference }
      - run: dotnet test --filter "CrossPlatformDeterminism"

  verify-macos:
    needs: generate-reference
    runs-on: macos-latest
    steps:
      - uses: actions/download-artifact@v4
        with: { name: reference }
      - run: dotnet test --filter "CrossPlatformDeterminism"
```

## Regression Seeds

Known seeds that have triggered bugs in the past, always tested:

| Seed | Bug ID | Description |
|------|--------|-------------|
| 42 | — | Default test seed |
| 0 | BUG-001 | Edge case: zero seed |
| 0xFFFFFFFF | BUG-012 | Max value seed overflow |
| 7777 | BUG-034 | Triggered collision edge case |
| 31337 | BUG-051 | Fusion during boss transition |

## Performance of Determinism Tests

| Test | Duration Target | Ticks/Second |
|------|----------------|-------------|
| 1,000 tick basic | < 500ms | 2,000+ |
| 10,000 tick extended | < 5s | 2,000+ |
| 4-player 1,000 tick | < 2s | 500+ (4 worlds) |
| Cross-platform reference gen | < 3s | — |
| Cross-platform verify | < 3s | — |
