# 29 — SUBSYSTEM DEPENDENCY MAP

## Build Order (Critical Path)

```
PHASE 1 (Parallel Tracks)
├── Track A: Custom ECS + Math (Pure C#)
│   ├── 1.1: Unity project setup (URP 2D, Input System, folder structure)
│   ├── 1.2: FixedPoint64.cs + FixedVec2.cs + FixedMath.cs + FixedRandom.cs
│   ├── 1.3: EotSWorld.cs, EotSEntity.cs, ComponentStorage.cs, SystemScheduler.cs
│   ├── 1.4: All Component structs (Transform, Velocity, Health, etc.)
│   └── 1.5: Bridge: SimulationRunner.cs, RenderProxy.cs, InputBridge.cs, EntitySpawner.cs
│
├── Track B: Backend
│   ├── 1.6: Supabase schema (players, servers, skill_tree_nodes, contributions)
│   └── 1.7: Redis session layer (Lua scripts, session service)
│
└── Track C: Proc-Gen (Rust)
    ├── 1.8: Rust crate structure, types, RNG
    └── 1.9: Zone graph generator + validator

PHASE 2 (Depends on Track A)
├── 2.1: Snapshot.cs, SnapshotRingBuffer.cs (needs ECS serialization)
├── 2.2: RollbackManager.cs, InputBuffer.cs, NetTransport.cs
├── 2.3: FusionStateMachine.cs, FusionInputRouter.cs
└── 2.4: MovementSystem.cs, GravitySystem.cs, CollisionSystem.cs (character controller)

PHASE 3 (Depends on Phase 2 + Track C)
├── 3.1: ProcGenBridge.cs ([DllImport] wrapper for Rust FFI)
├── 3.2: CombatSystem.cs, InfectionSystem.cs, BoonSystem.cs
├── 3.3: Fused class abilities + SporePlaySystem.cs
└── 3.4: RoomSpawner.cs + TilemapBuilder.cs (instantiate rooms from graph)

PHASE 4 (Parallel to Phase 2-3)
├── 4.1: FMOD for Unity integration + AudioManager.cs
├── 4.2: VOIP transport (Opus via native plugin, C# wrapper)
├── 4.3: SporeTalkDSP.cs (infection voice processing)
└── 4.4: NarratorManager.cs (trigger system + FMOD events)
```

## Key Unity-Specific Notes

- Track A produces **pure C# files with zero `using UnityEngine;`** in Core/ folder
- Bridge/ files are the ONLY place where UnityEngine is imported
- This means the ECS can be extracted to a standalone .NET project for server Option B
- Unity Test Runner EditMode tests cover all Core/ code without entering Play mode
- PlayMode tests cover Bridge/ integration (does RenderProxy actually move the SpriteRenderer?)

## System Dependency Table
| System | Depends On | Blocks |
|---|---|---|
| Unity project setup | Nothing | Everything |
| FixedPoint64 math (C#) | Nothing | ECS, Rust RNG compatibility |
| Custom ECS (C#) | FixedPoint64 | All simulation |
| Bridge layer | ECS + Unity project | Visible gameplay |
| Supabase schema | Nothing | Community Tree |
| Redis session | Supabase | Matchmaking, VOIP |
| Snapshot system | ECS serialization | Rollback |
| Rollback netcode | Snapshots | Multiplayer |
| Fusion state machine | Rollback | Fused classes |
| Rust proc-gen | FixedPoint64 (RNG compat) | Room spawning |
| Character controller | ECS systems | Combat |
| Combat system | Controller | Boons, Infection |
| Infection system | Combat | DSP, Narrator |
| FMOD integration | Unity project | Audio, Narrator |
| VOIP | Redis, Netcode | DSP |
| Room spawning | Proc-gen bridge + Tilemap | Playable zones |
