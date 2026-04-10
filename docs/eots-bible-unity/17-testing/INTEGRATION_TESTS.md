# Integration Tests

## Scope

Integration tests verify that multiple systems work together correctly. They run in
Unity PlayMode and may reference UnityEngine assemblies (Bridge, rendering, input).

## Assembly References

```
EotS.Tests.PlayMode
  → EotS.Core          (ECS, systems, math)
  → EotS.Bridge        (SimulationRunner, RenderProxy, InputBridge)
  → UnityEngine        (GameObjects, MonoBehaviour, etc.)
  → Unity.InputSystem  (Input simulation)
```

## Test Suites

### Bridge Integration

| Test | Description | Assertions |
|------|-------------|------------|
| SimRunner_TicksAt60Hz | SimulationRunner drives world at 60Hz | Tick count matches expected after N seconds |
| RenderProxy_SyncsPosition | ECS position reflects in Transform | FP64 → float conversion within 0.01 |
| RenderProxy_SyncsAnimation | ECS state drives Animator parameters | Correct animation state after attack |
| InputBridge_MapsActions | Unity InputAction → InputCommand | All button flags correctly mapped |
| EntitySpawner_CreatesGO | ECS entity spawn creates GameObject | GO exists with correct components |
| EntitySpawner_DestroysGO | ECS entity destroy removes GameObject | GO is null after destroy |

### Combat Integration

| Test | Description | Duration |
|------|-------------|----------|
| FullCombatSequence | Player attacks enemy, deals damage, enemy dies | 120 ticks |
| ComboSystem | Chain 5 light attacks, verify combo counter and damage scaling | 300 ticks |
| StatusEffectApply | Apply poison, verify tick damage over 5 seconds | 300 ticks |
| CriticalHit | Verify crit triggers at correct rate over 1000 attacks | 1000 attacks |
| Backstab | Position player behind enemy, attack, verify 2x damage | 60 ticks |
| HitstopSync | Verify hitstop pauses both attacker and target | 10 ticks |

### Fusion Integration

| Test | Steps | Assertions |
|------|-------|------------|
| FusionMerge | P1 requests, P2 accepts, merge occurs | Fused entity exists, individuals removed |
| FusionSeparate | Fuse → wait 5s → separate | Two entities restored, stats correct |
| FusionCombat | Fuse → attack enemy → verify combined damage | Damage = merged stats |
| FusionInfection | Fuse at different infection levels → verify average | Infection = (P1 + P2) / 2 |
| FusionDeath | Fuse → take lethal damage → both die | Both players dead |
| FusionRollback | Fuse → rollback to pre-fusion tick → verify unfused | Entities restored correctly |

### Network Integration

| Test | Setup | Assertions |
|------|-------|------------|
| TwoWorldSync | 2 EotSWorld instances, shared inputs | Identical checksums after 600 ticks |
| RollbackCorrectness | Inject artificial delay in world B | Both worlds converge after rollback |
| DesyncRecovery | Corrupt world B state, trigger resync | World B matches A after StateMsg |
| InputBufferRedundancy | Drop 50% of packets | Simulation still converges (redundant sends) |
| VoicePipeline | Encode → network sim → decode → DSP | Audio output matches expected waveform |

### Proc-Gen Integration

| Test | Description | Pass Criteria |
|------|-------------|--------------|
| ZoneGeneration | Generate zone with seed, verify structure | All rooms connected, boss room reachable |
| RoomInstantiation | Load room prefab, verify tilemap integrity | No missing tiles, doors aligned |
| Zone100Batch | Generate 100 zones, validate all | 100% pass rate |
| SeedDeterminism | Same seed → same zone layout | Byte-identical output |
| RoomPoolCoverage | Generate 1000 zones, verify all room templates used | Each template appears > 0 times |

### Community Tree Integration

| Test | Description | Method |
|------|-------------|--------|
| EchoDeposit | Submit echoes, verify tree state update | Mock Supabase endpoint |
| TreeStageTransition | Deposit enough echoes to trigger stage change | Verify stage + 1 |
| TreeUnlock | Stage change unlocks new content | Verify unlock flags |

## Test Infrastructure

### PlayMode Test Setup

```csharp
[UnitySetUp]
public IEnumerator SetUp()
{
    // Load minimal test scene
    yield return SceneManager.LoadSceneAsync("TestScene", LoadSceneMode.Single);
    
    // Create simulation runner
    _runner = new GameObject("SimRunner").AddComponent<SimulationRunner>();
    _runner.Initialize(new EotSWorld(seed: 42));
    
    yield return null;  // Wait one frame for initialization
}

[UnityTearDown]
public IEnumerator TearDown()
{
    Object.Destroy(_runner.gameObject);
    yield return SceneManager.LoadSceneAsync("Empty", LoadSceneMode.Single);
}
```

### Input Simulation

```csharp
public static InputCommand SimulateInput(
    int moveX = 0, int moveY = 0, params Button[] buttons)
{
    var cmd = new InputCommand { MoveX = (byte)(moveX + 1), MoveY = (byte)(moveY + 1) };
    foreach (var btn in buttons)
        cmd.ButtonFlags |= (ushort)(1 << (int)btn);
    return cmd;
}
```

## Test Schedule

| Suite | When | Duration |
|-------|------|----------|
| Bridge integration | Every CI (PR + push) | ~2 min |
| Combat integration | Every CI | ~1 min |
| Fusion integration | Every CI | ~1 min |
| Network integration | Every CI | ~3 min |
| Proc-gen integration | Nightly | ~5 min |
| Community Tree | Nightly | ~1 min |
| **Total (CI)** | — | **~7 min** |
| **Total (Nightly)** | — | **~13 min** |

## Failure Handling

| Failure Type | Action |
|-------------|--------|
| Assertion failure | Block PR merge, notify author |
| Timeout (> 30s per test) | Fail test, investigate hang |
| Crash during test | Capture dump, fail suite, P1 bug |
| Flaky test (passes on retry) | Quarantine test, investigate, fix within 48h |
| Desync in network test | Capture both world states, file determinism bug |
