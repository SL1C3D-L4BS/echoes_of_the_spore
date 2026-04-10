# 29 — SUBSYSTEM DEPENDENCY MAP

## Build Order (Critical Path)

```
PHASE 1 (Parallel Tracks)
├── Track A: ECS + Math
│   ├── 1.2: ECS Foundation
│   ├── 1.3: FixedPoint64 Math Library
│   └── 1.6: ECS ↔ UE5 Bridge
│
├── Track B: Backend
│   ├── 1.4: Supabase Schema
│   └── 1.5: Redis Session Layer
│
└── Track C: Proc-Gen
    ├── 3.1: Rust Data Types + RNG
    └── 3.2: Zone Graph Generator

PHASE 2 (Depends on Track A)
├── 2.1: Snapshot System (needs ECS)
├── 2.2: Rollback Netcode (needs Snapshots)
├── 2.3: Fusion Input Sync (needs Rollback)
└── 4.1: Character Controller (needs ECS + Bridge)

PHASE 3 (Depends on Phase 2)
├── 3.3: WASM/FFI Bridge (needs Generator)
├── 4.2: Combat + Infection (needs Controller)
├── 4.3: Fused Classes + Spore Plan (needs Fusion + Combat)
└── 6.1: Room Spawning (needs Bridge + Generator)

PHASE 4 (Depends on Phase 1 Track B)
├── 5.1: FMOD Integration
├── 5.2: VOIP Transport (needs Redis + Netcode)
├── 5.3: Infection DSP Chain (needs VOIP)
└── 5.4: DSP ↔ ECS Wiring (needs DSP + ECS)
```

## System Dependency Table

| System | Depends On | Blocks |
|---|---|---|
| ECS Foundation | Nothing | Everything (critical path root) |
| FixedPoint64 Math | Nothing | ECS, Proc-Gen RNG, all simulation |
| UE5 Bridge | ECS | Character Controller, Room Spawning |
| Supabase Schema | Nothing | Community Tree, Accounts |
| Redis Session | Supabase | Matchmaking, VOIP, Infection Sync |
| Snapshot System | ECS | Rollback Netcode |
| Rollback Netcode | Snapshots | Fusion Sync, Multiplayer |
| Fusion Input Sync | Rollback | Fused Classes, Spore Plan |
| Rust Proc-Gen | FixedPoint64 (compatible RNG) | Room Spawning |
| Character Controller | ECS, Bridge | Combat, Abilities |
| Combat System | Controller | Boon System, Infection |
| Infection System | Combat | DSP Chain, Narrator triggers |
| FMOD Integration | UE5 Project | All audio, Narrator |
| VOIP Transport | Redis, Netcode | DSP Chain |
| DSP Chain | VOIP | Spore-Talk (player-facing) |
| Room Spawning | Proc-Gen Bridge, Controller | Playable zones |
| Community Tree | Supabase | Canopy evolution, progression |
| Boon System | Combat | Build diversity |
| Narrator | FMOD, Infection | Atmospheric storytelling |
