# Regression Tests

## Overview

Regression tests ensure that previously fixed bugs do not reappear. Every P0/P1 bug
fix must include a regression test. Tests are tagged with the bug ID for traceability.

## Regression Test Registry

| Bug ID | System | Description | Test File |
|--------|--------|-------------|-----------|
| BUG-001 | Math | FP64 multiply overflow at max values | FixedPoint64Tests.cs |
| BUG-005 | ECS | Entity destroy during iteration causes skip | WorldTests.cs |
| BUG-012 | Netcode | Rollback past fusion transition corrupts state | RollbackTests.cs |
| BUG-018 | Combat | Backstab damage applied from front at certain angle | CombatSystemTests.cs |
| BUG-023 | Infection | Tier 75→76 transition skipped if exact HP threshold | InfectionSystemTests.cs |
| BUG-031 | Fusion | Separating during Spore Plan causes null entity | FusionSystemTests.cs |
| BUG-034 | ProcGen | Seed 7777 generates disconnected room graph | ProcGenTests.cs |
| BUG-041 | Snapshot | Deserialize of empty component array crashes | SerializationTests.cs |
| BUG-047 | Status | Poison + burn interaction causes negative HP | StatusEffectTests.cs |
| BUG-051 | Fusion | Fusion during boss phase transition desyncs | DeterminismTests.cs |
| BUG-058 | Audio | FMOD bank swap during zone transition causes pop | AudioIntegrationTests.cs |
| BUG-062 | Network | VoiceMsg with seq=0 treated as stale | InputBufferTests.cs |
| BUG-071 | Render | RenderProxy position drift after 10000+ ticks | RenderProxyTests.cs |
| BUG-078 | AI | Enemy stuck in wall after rollback | AISystemTests.cs |
| BUG-083 | UI | Health bar shows negative value on overkill | UITests.cs |

## Regression Test Format

```csharp
/// <summary>
/// Regression test for BUG-031: Separating during Spore Plan execution
/// caused a null entity reference because the fused entity was destroyed
/// before the plan completed.
/// Fixed in commit abc123 by deferring separation until plan completes.
/// </summary>
[Test]
[Category("Regression")]
[Property("BugId", "BUG-031")]
public void BUG031_SeparateDuringSporePlan_DoesNotCrash()
{
    var world = TestWorldFactory.CreateWithFusedPlayers(seed: 42);
    var fused = world.GetFusedEntity();
    
    // Start Spore Plan
    var input = new InputCommand { ButtonFlags = Button.SporePlan };
    world.SetInputForEntity(fused, input);
    world.Tick();
    
    Assert.IsTrue(world.GetComponent<SporePlan>(fused).IsExecuting);
    
    // Attempt separation during plan execution
    var separateInput = new InputCommand { ButtonFlags = Button.FusionSeparate };
    world.SetInputForEntity(fused, separateInput);
    
    // Should not crash; separation should be deferred
    Assert.DoesNotThrow(() => world.Tick());
    
    // Fused entity should still exist (separation deferred)
    Assert.IsTrue(world.EntityExists(fused));
    
    // After plan completes, separation should process
    for (int i = 0; i < 60; i++) // Tick through plan duration
        world.Tick();
    
    Assert.IsFalse(world.EntityExists(fused), 
        "Fused entity should be destroyed after deferred separation");
}
```

## Regression Test Categories

| Category | Count Target | CI Gate |
|----------|-------------|---------|
| Math regression | 20+ | Every CI |
| ECS regression | 15+ | Every CI |
| System regression | 30+ | Every CI |
| Netcode regression | 20+ | Every CI |
| Integration regression | 10+ | Every CI |
| Platform regression | 5+ | Nightly |
| **Total** | **100+** | — |

## Adding a Regression Test

When fixing a P0/P1 bug, follow this checklist:

| Step | Action |
|------|--------|
| 1 | Write test that reproduces the bug (must fail before fix) |
| 2 | Add `[Category("Regression")]` attribute |
| 3 | Add `[Property("BugId", "BUG-XXX")]` attribute |
| 4 | Add XML doc comment with bug description and fix summary |
| 5 | Verify test passes after fix |
| 6 | Add entry to regression test registry (this document) |
| 7 | PR must include both fix and test |

## Regression Test in CI

```yaml
# Runs as part of standard CI, tagged for easy filtering
- name: Run regression tests
  run: |
    dotnet test --filter "Category=Regression" \
      --logger "trx;LogFileName=regression.trx" \
      --results-directory TestResults/
```

## Flaky Regression Test Policy

| Situation | Action |
|-----------|--------|
| Regression test becomes flaky | P1 priority: fix within 48 hours |
| Cannot fix flaky test quickly | Quarantine (skip) with ticket, max 1 week |
| Flaky test unresolved after 1 week | Escalate to tech lead |
| Quarantined tests | Maximum 3 quarantined at any time |

## Historical Bug Analysis

Track patterns to prevent similar bugs:

| System | Bug Count (Cumulative) | Common Pattern |
|--------|----------------------|----------------|
| Math/FP64 | 8 | Overflow at boundary values |
| ECS | 6 | Entity lifecycle during iteration |
| Combat | 12 | Edge cases in angle/distance calculation |
| Fusion | 9 | State transitions during async operations |
| Netcode | 7 | Sequence number wrapping, timing |
| Serialization | 4 | Empty/null containers |
| Status Effects | 5 | Multi-effect interactions |
| AI | 3 | Rollback leaves AI in invalid state |
| Rendering | 4 | Float precision drift over time |
| UI | 3 | Edge case display values |

## Regression Coverage Report

Generated weekly, tracks:

| Metric | Target |
|--------|--------|
| P0 bugs with regression test | 100% |
| P1 bugs with regression test | 100% |
| P2 bugs with regression test | > 50% |
| Regression test pass rate | 100% (0 failures allowed) |
| Quarantined tests | < 3 |
| Avg regression test runtime | < 100ms per test |
| Total regression suite runtime | < 30s |
