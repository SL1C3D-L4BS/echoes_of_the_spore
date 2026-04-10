# Build Order — Dependency Graph

## Phase 1: Foundation (Months 1-4)

```
Track A (ECS):          Track B (Backend):       Track C (Proc-Gen):
FixedPoint64            Supabase Schema          eots-procgen-core (Rust)
    ↓                       ↓                        ↓
Vector2FP + AABB        Redis Session Layer      Zone Graph Generator
    ↓                       ↓                        ↓
EotSWorld + Components  BackendClient.cs         C FFI Bridge
    ↓                                                ↓
SystemPipeline                                   ProcGenBridge.cs
    ↓
MovementSystem
    ↓
CollisionSystem (SpatialHash)
    ↓
InputCommand + InputSystem
    ↓
SnapshotSerializer
    ↓
RollbackManager

Track D (Bridge):
SimulationRunner ← depends on EotSWorld
    ↓
RenderProxy ← depends on TransformComponent
    ↓
InputBridge ← depends on InputCommand
    ↓
EntitySpawner ← depends on EventBus
    ↓
URP 2D Setup + Pixel Perfect Camera
```

## Phase 2: Vertical Slice (Months 5-10)

```
CombatSystem ← depends on CollisionSystem, HealthComponent
    ↓
HitboxComponent + HitDetectionSystem
    ↓
ComboSystem ← depends on CombatSystem
    ↓
StatusEffectSystem ← depends on CombatSystem
    ↓
InfectionSystem ← depends on StatusEffects
    ↓
SilkGaugeComponent + HealingSystem
    ↓
Sporeling Class ← depends on CombatSystem, InputSystem
Rootwarden Class ← depends on CombatSystem, InputSystem
    ↓
FusionStateMachine ← depends on both classes
    ↓
Myco-Juggernaut (first Fused class)
    ↓
Zone 1 Rooms ← depends on ProcGenBridge + Tilemap
    ↓
Zone 1 Enemies (T1 + T2) ← depends on AISystem
    ↓
Spore Mother Boss ← depends on AISystem + CombatSystem
    ↓
UDP Transport ← depends on SnapshotSerializer
    ↓
Online Co-Op ← depends on RollbackManager + UDP
    ↓
FMOD Integration ← independent, parallel track
    ↓
Spore-Talk DSP Prototype ← depends on FMOD + InfectionSystem
```

## Phase 3: Alpha (Months 11-16)

```
Bloomcaster + Decayer Classes ← depends on CombatSystem
    ↓
Remaining 10 Fused Classes ← depends on all 4 base classes
    ↓
Echo Boon System ← depends on CombatSystem + StatusEffects
    ↓
Zones 2-8 (parallel, each depends on ProcGen + Tilemap + AISystem)
    ↓
8 Zone Bosses (each depends on AISystem + CombatSystem)
    ↓
Ecosystem Simulation ← depends on AISystem
    ↓
Community Skill Tree ← depends on BackendClient + Redis
    ↓
Heat System ← depends on all combat systems
    ↓
Echo Codex ← depends on all progression systems
    ↓
Mycelium Voice ← depends on AudioManager + trigger events
    ↓
AI Director (Zone 8) ← depends on all AI + analytics
```

## Critical Path

The longest dependency chain determines minimum timeline:

```
FixedPoint64 → EotSWorld → MovementSystem → CollisionSystem
  → CombatSystem → Classes → FusionSystem → Zone 1 → Online Co-Op
  → Remaining Zones → Beta → Polish → Launch

Estimated: 22-24 months minimum
```

## Parallel Tracks (Can Be Built Independently)

| Track | Dependencies | Can Start |
|-------|-------------|-----------|
| Rust Proc-Gen | None (standalone crate) | Month 1 |
| FMOD Audio | None (standalone integration) | Month 1 |
| UI System | SimulationRunner (Bridge) | Month 2 |
| Supabase Schema | None | Month 1 |
| Art Production | Art direction locked | Month 2 |
| Narrative Writing | Game design locked | Month 3 |
