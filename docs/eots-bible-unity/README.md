# ECHOES OF THE SPORE — MASTER GAME BIBLE

## Production-Grade Technical Design Library v2026.3 — Unity 6.3 LTS Edition

**Studio:** SL1C3D-L4BS
**Lead Architect:** Michael A. Cooper
**Date:** April 2026
**Engine:** Unity 6.3 LTS (Runtime Fee Cancelled — Free under $200K, Pro $2,200/yr)
**Classification:** CONFIDENTIAL

---

## Engine Decision

This project uses **Unity 6.3 LTS** as its engine.

**Rationale:**
- Runtime fee cancelled — no per-install charges, no royalties on revenue
- Free Personal tier up to $200K revenue; Pro at $2,200/year (negligible vs budget)
- Proven for 2D Metroidvanias: Hollow Knight, Dead Cells (original), Silksong all ship on Unity
- Native C# — Claude Code generates excellent C# with full IntelliSense-level accuracy
- Mature 2D pipeline: SpriteRenderer, Tilemap, 2D Physics, Cinemachine 2D, URP 2D Renderer
- Unity Netcode for GameObjects + custom rollback layer
- Native plugin support for C/C++ (Rust FFI for proc-gen)
- Console export: PS5, Xbox, Switch built-in (no third-party needed)
- Claude Code MCP connection verified and operational
- Unity AI tools (Sentis, Assistant) available in 6.3 LTS for future features
- LTS support through December 2027

## Two-Layer Architecture

**C# (Unity scripts)** — everything:
- Game simulation (custom deterministic ECS in pure C#, NOT Unity DOTS)
- Fixed-point math (custom FixedPoint64 struct in C#)
- Rollback netcode (custom, input prediction + resimulation)
- Fusion state machine
- Rendering (SpriteRenderer, Tilemap, UI Toolkit or Canvas)
- Input (Unity Input System)
- Audio management (FMOD integration via FMOD for Unity plugin)
- Scene management
- Backend communication (UnityWebRequest → Supabase REST, Redis via session service)

**Rust (native plugin via C FFI)** — procedural generation only:
- Proc-gen crate compiled to native .so/.dll/.dylib
- Loaded via Unity's NativePlugin system
- Called from C# via [DllImport]

**Why NOT Unity DOTS/ECS?**
Unity's DOTS ECS is powerful but NOT deterministic across platforms by default (uses float math). Our game requires cross-platform determinism for rollback netcode. We build a lightweight custom ECS in pure C# with FixedPoint64 math that guarantees bit-identical simulation on all platforms. This custom ECS is ~2,000 lines of C# — much simpler than adopting the full DOTS stack.

## Hard Constraints

| Constraint | Value | Rationale |
|---|---|---|
| Engine | Unity 6.3 LTS | Proven, no royalties, C#, console export |
| Render Pipeline | URP (2D Renderer) | Lightweight, 2D-optimized, shader graph |
| Simulation tick | 60 Hz fixed | Rollback determinism |
| Simulation math | FixedPoint64 (32.32 int64) in C# | Cross-platform determinism |
| Primary language | C# | Unity native, Claude Code fluent |
| Max entities/room | 4,096 | Custom ECS storage bound |
| Snapshot serialize | < 500 μs / 500 entities | Rollback budget |
| Network latency | < 50 ms RTT | 4P fusion |
| Rollback window | 10 ticks (166 ms) | Re-sim ceiling |
| Max session players | 4 | Fusion design |
| Max server players | 10,000 | Community Tree scale |
| Render FPS | Variable (60+ target) | Interpolation from sim |
| Audio DSP frame | 20 ms (960 samples @ 48 kHz) | Opus alignment |
| Room gen time | < 100 ms | Load budget |

## Project Structure (Unity 6.3 LTS)

```
EchoesOfTheSpore/                         # Unity project root
├── Assets/
│   ├── _Project/                         # All game-specific assets
│   │   ├── Scenes/
│   │   │   ├── MainMenu.unity
│   │   │   ├── CanopyHub.unity
│   │   │   ├── Delve.unity              # The run scene (rooms loaded additively)
│   │   │   ├── RiteArena.unity
│   │   │   └── Test/
│   │   │       └── ProofOfLife.unity
│   │   ├── Scripts/
│   │   │   ├── Core/
│   │   │   │   ├── ECS/                 # Custom deterministic ECS
│   │   │   │   │   ├── EotSWorld.cs
│   │   │   │   │   ├── EotSEntity.cs
│   │   │   │   │   ├── ComponentStorage.cs
│   │   │   │   │   ├── SystemScheduler.cs
│   │   │   │   │   └── Components/
│   │   │   │   │       ├── TransformComponent.cs
│   │   │   │   │       ├── VelocityComponent.cs
│   │   │   │   │       ├── HealthComponent.cs
│   │   │   │   │       ├── InfectionComponent.cs
│   │   │   │   │       ├── PlayerInputComponent.cs
│   │   │   │   │       ├── FusionComponent.cs
│   │   │   │   │       └── ColliderComponent.cs
│   │   │   │   ├── Math/
│   │   │   │   │   ├── FixedPoint64.cs  # 32.32 fixed-point struct
│   │   │   │   │   ├── FixedVec2.cs
│   │   │   │   │   ├── FixedMath.cs     # Sin, cos, atan2 lookup tables
│   │   │   │   │   └── FixedRandom.cs   # xoshiro256** PRNG
│   │   │   │   ├── Netcode/
│   │   │   │   │   ├── Snapshot.cs
│   │   │   │   │   ├── SnapshotRingBuffer.cs
│   │   │   │   │   ├── RollbackManager.cs
│   │   │   │   │   ├── InputBuffer.cs
│   │   │   │   │   └── NetTransport.cs
│   │   │   │   └── Systems/             # ECS gameplay systems
│   │   │   │       ├── MovementSystem.cs
│   │   │   │       ├── GravitySystem.cs
│   │   │   │       ├── CollisionSystem.cs
│   │   │   │       ├── CombatSystem.cs
│   │   │   │       ├── InfectionSystem.cs
│   │   │   │       ├── FusionStateMachine.cs
│   │   │   │       └── FusionInputRouter.cs
│   │   │   ├── Bridge/                  # Unity ↔ ECS adapters
│   │   │   │   ├── SimulationRunner.cs  # MonoBehaviour, ticks ECS at 60Hz
│   │   │   │   ├── RenderProxy.cs       # Reads ECS, updates SpriteRenderer
│   │   │   │   ├── InputBridge.cs       # Unity Input System → ECS
│   │   │   │   └── EntitySpawner.cs     # ECS entity created → spawn Unity GO
│   │   │   ├── ProcGen/
│   │   │   │   ├── ProcGenBridge.cs     # C# [DllImport] wrapper for Rust FFI
│   │   │   │   └── ZoneGraphData.cs     # C# structs mirroring Rust output
│   │   │   ├── Audio/
│   │   │   │   ├── AudioManager.cs
│   │   │   │   ├── NarratorManager.cs
│   │   │   │   └── SporeTalkDSP.cs      # Infection voice processing
│   │   │   ├── UI/
│   │   │   │   ├── HUDController.cs
│   │   │   │   ├── InfectionDisplay.cs
│   │   │   │   ├── BoonSelectUI.cs
│   │   │   │   └── CommunityTreeUI.cs
│   │   │   ├── World/
│   │   │   │   ├── RoomSpawner.cs
│   │   │   │   ├── TilemapBuilder.cs
│   │   │   │   └── CanopyManager.cs
│   │   │   ├── Network/
│   │   │   │   ├── NetworkManager.cs
│   │   │   │   ├── MatchmakingService.cs
│   │   │   │   └── BackendClient.cs     # Supabase + Redis HTTP calls
│   │   │   └── Data/
│   │   │       ├── BoonDatabase.cs      # ScriptableObject registry
│   │   │       ├── EnemyDatabase.cs
│   │   │       └── ZoneConfig.cs
│   │   ├── Art/
│   │   │   ├── Sprites/
│   │   │   ├── Tilesets/
│   │   │   ├── Parallax/
│   │   │   ├── UI/
│   │   │   └── Shaders/                 # URP Shader Graph + HLSL
│   │   │       ├── InfectionPulse.shadergraph
│   │   │       ├── FossilMuralReveal.shadergraph
│   │   │       └── SporeParticle.shadergraph
│   │   ├── Audio/
│   │   │   └── FMODBanks/
│   │   ├── Prefabs/
│   │   │   ├── Player/
│   │   │   ├── Enemies/
│   │   │   ├── Rooms/
│   │   │   └── UI/
│   │   ├── ScriptableObjects/
│   │   │   ├── Boons/
│   │   │   ├── Enemies/
│   │   │   ├── Zones/
│   │   │   └── Functions/
│   │   └── Fonts/
│   ├── Plugins/
│   │   ├── FMOD/                        # FMOD for Unity plugin
│   │   └── Native/
│   │       ├── x86_64/
│   │       │   └── libeots_procgen.so   # Rust proc-gen (Linux)
│   │       └── x86_64/
│   │           └── eots_procgen.dll     # Rust proc-gen (Windows)
│   └── Settings/
│       ├── URP-2D-Renderer.asset
│       └── URP-2D-RendererData.asset
├── Packages/
│   └── manifest.json
├── ProjectSettings/
│   ├── ProjectSettings.asset
│   ├── InputManager.asset              # Legacy (disabled)
│   └── InputSystem.asset               # Unity Input System (active)
├── docs/                               # This bible
├── rust/                               # Rust proc-gen (external to Unity)
│   └── crates/
│       ├── eots-procgen-core/
│       ├── eots-procgen-generator/
│       ├── eots-procgen-ffi/
│       └── eots-procgen-wasm/
├── backend/
│   ├── supabase/
│   └── redis/
└── .gitignore
```

## Naming Conventions

| Domain | Convention | Example |
|---|---|---|
| ECS Components (C#) | PascalCase + Component | `TransformComponent` |
| ECS Systems (C#) | PascalCase + System | `MovementSystem` |
| MonoBehaviours | PascalCase | `SimulationRunner` |
| ScriptableObjects | SO_ prefix or Database suffix | `BoonDatabase` |
| Scenes | PascalCase.unity | `MainMenu.unity` |
| Shaders | PascalCase.shadergraph | `InfectionPulse.shadergraph` |
| Prefabs | PascalCase | `Player_Sporeling.prefab` |
| Rust Crates | eots-procgen-{module} | `eots-procgen-core` |
| Redis Keys | entity:{id}:field | `session:abc:infection:p1` |
| Supabase Tables | snake_case | `skill_tree_nodes` |
| C# namespaces | EotS.{Module} | `EotS.Core.ECS`, `EotS.Bridge` |
