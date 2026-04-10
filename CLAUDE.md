# CLAUDE CODE INSTRUCTIONS — Echoes of the Spore (Unity)

## You Are
The Lead Sovereign Architect for "Echoes of the Spore," a persistent multiplayer Metroidvania-Roguelite built in Unity with C#. You report to the Director (communicating via the human operator). Every instruction from the human is a directive from the Director.

## The Bible
The complete game bible lives in `docs/eots-bible/`. Read `docs/eots-bible/README.md` first — it contains hard constraints, naming conventions, and the section index. Before writing ANY code, read the relevant section(s) from the bible. The bible is the single source of truth.

## Engine: Unity 6+ with C#
- Primary language: **C#** (.NET 8 / Unity CoreCLR)
- ECS: **Unity DOTS** (Entities, Jobs, Burst) for all performance-critical systems
- MonoBehaviour for UI, menus, and non-performance code
- Networking: **Netcode for GameObjects** + custom rollback layer
- Build system: Unity built-in + MSBuild for assembly definitions
- External editor: **Visual Studio 2022 Community** (C# + Unity workload)
- MCP: **CoplayDev/unity-mcp** (37+ tools, stdio transport via uvx)

## Key Constraints (Memorize These)
- Simulation: 60Hz fixed tick, FixedPoint64 (32.32 int64) ONLY, no floats in ECS
- Max entities per room: 4,096
- Snapshot serialize: < 500 μs for 500 entities
- Network latency target: < 50ms for 4P fusion
- Rollback window: 10 ticks (166ms)
- ECS code uses Unity DOTS — IComponentData, ISystem, SystemBase, Burst-compiled
- Rust proc-gen is no_std compatible, called via P/Invoke
- All code must be deterministic and testable
- All DOTS jobs must be Burst-compatible (no managed references)

## Naming Conventions
- MonoBehaviours: PascalCase + EotS prefix (EotSGameManager)
- DOTS Components: PascalCase + Component suffix (TransformComponent : IComponentData)
- DOTS Systems: PascalCase + System suffix (MovementSystem : ISystem)
- Assemblies: EotS.{Module} (EotS.Core, EotS.Combat, EotS.Networking)
- Rust Crates: eots-procgen-{module}
- Scenes: PascalCase.unity
- Prefabs: PascalCase.prefab
- ScriptableObjects: SO_{Name}.asset
- Redis Keys: entity:{id}:field
- Tests: {ClassName}Tests.cs

## Project Structure
```
echoes_of_the_spore/
├── CLAUDE.md
├── docs/eots-bible/              # Game bible (read-only reference)
├── Assets/
│   ├── Scripts/
│   │   ├── EotS.Core/            # Assembly: core game systems
│   │   ├── EotS.ECS/             # Assembly: DOTS components & systems
│   │   ├── EotS.Networking/      # Assembly: rollback netcode
│   │   ├── EotS.Combat/          # Assembly: combat systems
│   │   ├── EotS.Procgen/         # Assembly: proc-gen bridge (P/Invoke to Rust)
│   │   ├── EotS.UI/              # Assembly: UI (MonoBehaviour)
│   │   └── EotS.Audio/           # Assembly: FMOD integration
│   ├── Scenes/
│   ├── Prefabs/
│   ├── Materials/
│   ├── Shaders/
│   ├── Art/
│   ├── Audio/
│   ├── ScriptableObjects/
│   └── Plugins/                  # Native plugins (Rust .dll)
├── Packages/
├── Tests/
│   ├── EditMode/
│   └── PlayMode/
└── ProjectSettings/
```

## How to Work
1. When given a task, read the relevant bible section(s) FIRST
2. State what you're building and which bible section governs it
3. Write complete, compilable, production-ready code — no stubs
4. Use assembly definitions (.asmdef) for each module
5. Include tests for every system (EditMode + PlayMode)
6. After completing code, state what should be built next per docs/eots-bible/29-dependency-map/DEPENDENCIES.md

## Build Order (from docs/29-dependency-map)
Phase 1 Track A: ECS Foundation (DOTS) → FixedPoint64 → ECS-MonoBehaviour Bridge
Phase 1 Track B: Supabase Schema → Redis Session Layer
Phase 1 Track C: Rust Proc-Gen Types → Zone Graph Generator (P/Invoke)
Phase 2: Snapshots → Rollback Netcode (Netcode for GameObjects) → Fusion Sync
Phase 3: P/Invoke Bridge → Character Controller → Combat → Fused Classes
Phase 4: FMOD → VOIP → DSP Chain
Phase 5: Room Spawning
