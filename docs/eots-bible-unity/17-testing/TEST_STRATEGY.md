# Test Strategy

## Test Pyramid

```
                    ╱╲
                   ╱  ╲
                  ╱ E2E╲          5 tests (PlayMode integration)
                 ╱──────╲
                ╱ Integr. ╲       20 tests (Bridge + ECS)
               ╱────────────╲
              ╱    Unit Tests  ╲   200+ tests (Core/)
             ╱──────────────────╲
```

## Unit Tests (EditMode — Core/)

**Location:** `Tests/EditMode/`
**Assembly:** References `EotS.Core` only (NO Unity deps)
**Runner:** Unity Test Framework (NUnit)

| Module | Test Count Target | Focus |
|--------|------------------|-------|
| FixedPoint64 | 50+ | Arithmetic, overflow, precision, edge cases |
| Vector2FP | 20+ | Normalize, distance, dot product |
| Xoshiro256 | 10+ | Sequence verification vs Rust output |
| EotSWorld | 20+ | Entity CRUD, component storage, queries |
| MovementSystem | 15+ | Gravity, terminal velocity, collision |
| CombatSystem | 25+ | Damage formula, crit, backstab, status |
| StatusEffects | 16+ | Each effect × apply/tick/expire/interact |
| InfectionSystem | 10+ | Tier transitions, stat multipliers |
| FusionSystem | 15+ | State machine transitions, stat merging |
| SnapshotSerializer | 10+ | Serialize/deserialize, size, checksum |
| RollbackManager | 15+ | Rollback/resimulate, input buffer |
| **Total** | **200+** | — |

### Determinism Tests (Critical)

```csharp
[Test]
public void Simulation_IsDeterministic_Over1000Ticks()
{
    var world1 = CreateWorldWithInputs(seed: 42, ticks: 1000);
    var world2 = CreateWorldWithInputs(seed: 42, ticks: 1000);

    var hash1 = world1.ComputeChecksum();
    var hash2 = world2.ComputeChecksum();

    Assert.AreEqual(hash1, hash2,
        "Simulation must produce identical state for identical inputs");
}

[Test]
public void Xoshiro256_MatchesRustOutput()
{
    var rng = new Xoshiro256StarStar(seed: 12345);
    var expected = LoadRustReferenceValues("xoshiro_12345.bin");

    for (int i = 0; i < 10000; i++)
        Assert.AreEqual(expected[i], rng.NextU64(),
            $"Mismatch at index {i}");
}
```

## Integration Tests (PlayMode — Bridge/)

**Location:** `Tests/PlayMode/`
**Assembly:** References `EotS.Core` + `EotS.Bridge` + UnityEngine

| Module | Test Count | Focus |
|--------|-----------|-------|
| SimulationRunner | 5 | Tick rate, interpolation, world ownership |
| RenderProxy | 5 | FP64→float conversion, visual position |
| InputBridge | 5 | Input System action → InputCommand |
| EntitySpawner | 5 | Create/destroy GameObject sync |
| **Total** | **20** | — |

## E2E Tests (PlayMode — Full Pipeline)

| Test | Description |
|------|-------------|
| Full Delve | Spawn player, enter zone, kill enemies, collect boon, clear boss |
| Fusion Flow | Two players fuse, fight, separate |
| Rollback Correctness | Simulate network delay, verify rollback produces correct state |
| Proc-Gen Validation | Generate 100 zones, verify all pass validation |
| Community Tree | Contribute Echoes, verify tree depth updates |

## CI/CD Pipeline

```yaml
# .github/workflows/tests.yml
name: Tests
on: [push, pull_request]
jobs:
  unit-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: game-ci/unity-test-runner@v4
        with:
          testMode: EditMode
          projectPath: eots

  integration-tests:
    runs-on: ubuntu-latest
    needs: unit-tests
    steps:
      - uses: game-ci/unity-test-runner@v4
        with:
          testMode: PlayMode
          projectPath: eots

  rust-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: cd rust && cargo test --all

  determinism:
    runs-on: ubuntu-latest
    needs: [unit-tests, rust-tests]
    steps:
      - run: cd rust && cargo test determinism_crossplatform
      - uses: game-ci/unity-test-runner@v4
        with:
          testMode: EditMode
          customParameters: -testFilter "Determinism"
```

## Coverage Target

| Module | Coverage Target |
|--------|----------------|
| Core/Math/ | 95% |
| Core/ECS/ | 90% |
| Core/Systems/ | 85% |
| Core/Netcode/ | 85% |
| Bridge/ | 70% |
| UI/ | 50% |
