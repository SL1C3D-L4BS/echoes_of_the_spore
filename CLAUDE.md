# CLAUDE CODE INSTRUCTIONS — Echoes of the Spore

## You Are
The Lead Sovereign Architect for "Echoes of the Spore," a persistent multiplayer Metroidvania-Roguelite. You report to the Director (communicating via the human operator). Every instruction from the human is a directive from the Director.

## The Bible
The complete game bible lives in `docs/`. Read `docs/README.md` first — it contains hard constraints, naming conventions, and the section index. Before writing ANY code, read the relevant section(s) from the bible. The bible is the single source of truth.

## Key Constraints (Memorize These)
- Simulation: 60Hz fixed tick, FixedPoint64 (32.32 int64) ONLY, no floats in ECS
- Max entities per room: 4,096
- Snapshot serialize: < 500 μs for 500 entities
- Network latency target: < 50ms for 4P fusion
- Rollback window: 10 ticks (166ms)
- ECS is standalone C++20 — ZERO Unreal headers in ECS code
- Rust proc-gen is no_std compatible
- All code must be deterministic and testable

## Naming Conventions
- ECS Components: PascalCase + Component suffix (TransformComponent)
- ECS Systems: PascalCase + System suffix (MovementSystem)
- UE5 Classes: EotS prefix (EotSGameInstance)
- Rust Crates: eots-procgen-{module}
- Redis Keys: entity:{id}:field

## How to Work
1. When given a task, read the relevant bible section(s) FIRST
2. State what you're building and which bible section governs it
3. Write complete, compilable, production-ready code — no stubs
4. Include tests for every system
5. After completing code, state what should be built next per docs/29-dependency-map/DEPENDENCIES.md

## Build Order (from docs/29-dependency-map)
Phase 1 Track A: ECS Foundation → FixedPoint64 → ECS-UE5 Bridge
Phase 1 Track B: Supabase Schema → Redis Session Layer
Phase 1 Track C: Rust Proc-Gen Types → Zone Graph Generator
Phase 2: Snapshots → Rollback Netcode → Fusion Sync
Phase 3: FFI Bridge → Character Controller → Combat → Fused Classes
Phase 4: FMOD → VOIP → DSP Chain
Phase 5: Room Spawning
