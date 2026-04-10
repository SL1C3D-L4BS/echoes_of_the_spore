# Unit Test Patterns

## Framework

| Component | Choice |
|-----------|--------|
| Runner | Unity Test Framework (NUnit 3.x) |
| Assertions | NUnit Assert + custom EotS assertions |
| Assembly | `EotS.Tests.EditMode` references `EotS.Core` only |
| No Unity deps | Core tests must compile without UnityEngine.* |
| Naming | `{System}_{Method}_{Scenario}_{Expected}` |

## Test Organization

```
Tests/
  EditMode/
    Math/
      FixedPoint64Tests.cs
      FixedVec2Tests.cs
      Xoshiro256Tests.cs
    ECS/
      WorldTests.cs
      EntityTests.cs
      ComponentStorageTests.cs
      QueryTests.cs
      SerializationTests.cs
    Systems/
      MovementSystemTests.cs
      CombatSystemTests.cs
      DamageSystemTests.cs
      StatusEffectSystemTests.cs
      InfectionSystemTests.cs
      FusionSystemTests.cs
      AISystemTests.cs
      ProjectileSystemTests.cs
    Netcode/
      SnapshotTests.cs
      RollbackTests.cs
      InputBufferTests.cs
      ChecksumTests.cs
    Determinism/
      DeterminismTests.cs
      CrossPlatformDeterminismTests.cs
```

## Common Test Patterns

### Arrange-Act-Assert (Standard)

```csharp
[Test]
public void DamageSystem_ApplyDamage_ReducesHP()
{
    // Arrange
    var world = TestWorldFactory.Create();
    var entity = world.CreateEntity();
    world.AddComponent(entity, new Health { Current = 100, Max = 100 });
    world.AddComponent(entity, new DamageEvent { Amount = 25, Source = -1 });

    // Act
    DamageSystem.Tick(world);

    // Assert
    Assert.AreEqual(75, world.GetComponent<Health>(entity).Current);
}
```

### Golden Reference (Math)

```csharp
[TestCaseSource(nameof(GoldenFixedPointData))]
public void FixedPoint64_Multiply_MatchesGolden(long a, long b, long expected)
{
    var result = FixedPoint64.Multiply(
        FixedPoint64.FromRaw(a), 
        FixedPoint64.FromRaw(b));
    Assert.AreEqual(expected, result.Raw, 
        $"FP64 multiply mismatch: {a} * {b}");
}

static IEnumerable<TestCaseData> GoldenFixedPointData()
{
    // Generated from Python decimal module
    yield return new TestCaseData(65536L, 65536L, 65536L);  // 1.0 * 1.0 = 1.0
    yield return new TestCaseData(131072L, 32768L, 65536L); // 2.0 * 0.5 = 1.0
    // ... 250+ cases from golden reference file
}
```

### State Machine Coverage

```csharp
[TestCase(FusionState.Idle, FusionInput.RequestMerge, FusionState.Requesting)]
[TestCase(FusionState.Requesting, FusionInput.PartnerAccept, FusionState.Merging)]
[TestCase(FusionState.Fused, FusionInput.RequestSeparate, FusionState.Separating)]
[TestCase(FusionState.Fused, FusionInput.InfectionOverload, FusionState.ForcedSeparation)]
public void FusionSystem_StateTransition(
    FusionState initial, FusionInput input, FusionState expected)
{
    var state = new FusionComponent { State = initial };
    FusionSystem.ProcessInput(ref state, input);
    Assert.AreEqual(expected, state.State);
}
```

### Boundary Value Testing

```csharp
[TestCase(0, 0)]          // Minimum
[TestCase(1, 1)]          // Just above min
[TestCase(10, 10)]        // Tier boundary
[TestCase(11, 11)]        // Just past tier 1
[TestCase(50, 50)]        // Mid-range
[TestCase(75, 75)]        // Tier boundary
[TestCase(99, 99)]        // Just below max
[TestCase(100, 100)]      // Maximum
[TestCase(-1, 0)]         // Below range (clamp)
[TestCase(101, 100)]      // Above range (clamp)
public void InfectionSystem_SetLevel_ClampsCorrectly(int input, int expected)
{
    var comp = new InfectionComponent();
    InfectionSystem.SetLevel(ref comp, input);
    Assert.AreEqual(expected, comp.Level);
}
```

## Custom Assertions

```csharp
public static class EotSAssert
{
    public static void FixedPointEqual(FixedPoint64 expected, FixedPoint64 actual, 
        string message = "")
    {
        Assert.AreEqual(expected.Raw, actual.Raw, 
            $"FP64 mismatch: expected {expected} got {actual}. {message}");
    }

    public static void WorldStateEqual(EotSWorld a, EotSWorld b)
    {
        Assert.AreEqual(a.EntityCount, b.EntityCount, "Entity count mismatch");
        Assert.AreEqual(a.ComputeChecksum(), b.ComputeChecksum(), 
            "World checksum mismatch");
    }

    public static void NoByteDiff(byte[] a, byte[] b, string label = "")
    {
        Assert.AreEqual(a.Length, b.Length, $"{label}: length mismatch");
        for (int i = 0; i < a.Length; i++)
            Assert.AreEqual(a[i], b[i], $"{label}: byte diff at offset {i}");
    }
}
```

## Test Data Management

| Data Type | Source | Location |
|-----------|--------|----------|
| Golden math values | Python script output | `TestData/golden_fp64.bin` |
| Xoshiro256 reference | Rust reference impl | `TestData/xoshiro_seeds.bin` |
| Replay inputs | Recorded gameplay | `TestData/replays/` |
| Snapshot binaries | Captured from running game | `TestData/snapshots/` |

## Test Performance Targets

| Test Suite | Max Duration | Test Count |
|-----------|-------------|-----------|
| Math (FixedPoint64 + Vector) | 5s | 300+ |
| ECS (CRUD + queries) | 3s | 70+ |
| Systems (all) | 10s | 150+ |
| Netcode (snapshot + rollback) | 5s | 50+ |
| Determinism | 15s | 5 |
| **Total EditMode** | **< 40s** | **575+** |

## Anti-Patterns to Avoid

| Anti-Pattern | Correct Approach |
|-------------|------------------|
| Using `Time.deltaTime` in tests | Use fixed tick, pass explicit dt |
| Testing private methods | Test through public API |
| Shared mutable state between tests | Fresh world per test via factory |
| Asserting floats with `==` | Use FixedPoint64, assert raw bits |
| Test depends on test order | Each test fully independent |
| Ignoring edge cases | Test min, max, boundaries, overflow |
| No assertion message | Always include context in message |
