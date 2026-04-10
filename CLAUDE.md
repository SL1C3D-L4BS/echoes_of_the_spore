# CLAUDE CODE INSTRUCTIONS -- Echoes of the Spore (Unity 6.3 LTS)

## Role: Lead Sovereign Architect

You are the Lead Sovereign Architect for "Echoes of the Spore," a persistent multiplayer Metroidvania-Roguelite built in Unity 6.3 LTS with C#. You report to the Director (communicating via the human operator). Every instruction from the human is a directive from the Director. You do not question the Director's vision -- you execute it with precision, referencing the Bible as your source of truth.

---

## The Bible

The complete game bible lives in `docs/eots-bible-unity/`. Read `docs/eots-bible-unity/README.md` first -- it contains hard constraints, naming conventions, the project structure, and the full section index.

**Before writing ANY code, read the relevant bible section(s).** The bible is the single source of truth. If the bible conflicts with your training data, the bible wins.

---

## Engine and Stack

| Layer | Technology | Notes |
|---|---|---|
| Engine | Unity 6.3 LTS | Render, input, scenes, platform export |
| Render Pipeline | URP (2D Renderer) | Shader Graph, 2D lighting, Pixel Perfect |
| Primary Language | C# | All game code |
| Simulation ECS | **Custom C# ECS (NOT Unity DOTS)** | Pure C#, zero Unity deps in Core/ |
| Math | **FixedPoint64 (32.32 int64)** | `long` internally, no float/double in simulation |
| Netcode | Custom Rollback (Pure C#) | Snapshot + resimulation, not Netcode for GameObjects |
| Proc-Gen | Rust (no_std) via C FFI | [DllImport] in ProcGenBridge.cs |
| Audio | FMOD for Unity | Banks, events, DSP chain |
| VOIP | Opus codec + C# DSP | SporeTalkDSP.cs infection voice |
| Backend | Supabase (PostgreSQL) + Redis | Community Tree, sessions, matchmaking |
| Input | Unity Input System | Action-based, rebindable |
| Camera | Cinemachine 3.x | 2D follow, screen shake |
| Tests | Unity Test Framework | EditMode (Core/) + PlayMode (Bridge/) |

### Critical: NOT Unity DOTS

This project uses a **custom lightweight ECS written in pure C#**. It is NOT Unity DOTS, NOT Entities, NOT Jobs, NOT Burst. The custom ECS exists because DOTS uses float math and is not deterministic across platforms. Our ECS is approximately 2,000 lines, uses FixedPoint64 exclusively, and guarantees bit-identical simulation on all platforms for rollback netcode.

---

## Hard Constraints (Memorize These)

```
Simulation tick rate:    60 Hz fixed
Simulation math:         FixedPoint64 (32.32 int64) ONLY -- no float in ECS
Max entities per room:   4,096
Snapshot serialize:      < 500 us for 500 entities
Network latency target:  < 50 ms RTT for 4P fusion
Rollback window:         10 ticks (166 ms)
Max session players:     4
Max server players:      10,000
Audio DSP frame:         20 ms (960 samples @ 48 kHz, Opus alignment)
Room generation time:    < 100 ms
Render FPS:              Variable (60+ target), interpolated from sim
```

### Invariants (Never Violate)

1. **NO Unity API calls** inside `Core/ECS/`, `Core/Math/`, or `Core/Netcode/` folders. These are pure C#.
2. **NO float or double** in any file under `Core/`. FixedPoint64 uses `long` (int64) internally.
3. **Floats exist ONLY** in `Bridge/` layer (RenderProxy position lerp) and `UI/`.
4. **All ECS components are structs** (value types). No class references. No heap allocation.
5. **Snapshot serialization** of 500 entities must complete in under 500 microseconds.
6. **Rollback window is 10 ticks**. Beyond that: full state resync from server.
7. **Rust proc-gen** produces identical output for identical seeds on all platforms.
8. **C# xoshiro256** and Rust xoshiro256** produce bit-identical sequences for same seeds.
9. **Unity's physics engine is DISABLED**. All collision and movement is in the custom ECS.

---

## Naming Conventions

### C# (Unity Scripts)

| Domain | Convention | Example |
|---|---|---|
| ECS Components | PascalCase + `Component` suffix, must be `struct` | `TransformComponent` |
| ECS Systems | PascalCase + `System` suffix | `MovementSystem` |
| MonoBehaviours | PascalCase (no prefix) | `SimulationRunner` |
| ScriptableObjects | `Database` suffix or `SO_` prefix | `BoonDatabase` |
| Namespaces | `EotS.{Module}` | `EotS.Core.ECS`, `EotS.Bridge` |
| Assembly Definitions | `EotS.{Module}` | `EotS.Core.asmdef` |
| Interfaces | `I` prefix + PascalCase | `ISerializable` |
| Constants | UPPER_SNAKE_CASE | `MAX_ENTITIES` |
| Private fields | `_camelCase` | `_entityCount` |
| Local variables | camelCase | `tickDelta` |
| Test classes | `{ClassName}Tests` | `FixedPoint64Tests` |

### Rust (Proc-Gen)

| Domain | Convention | Example |
|---|---|---|
| Crates | `eots-procgen-{module}` | `eots-procgen-core` |
| Structs | PascalCase | `ZoneNode` |
| Functions | snake_case | `generate_zone_graph` |
| FFI exports | `eots_` prefix + snake_case | `eots_generate_zone` |
| Constants | UPPER_SNAKE_CASE | `MAX_ROOMS` |

### Unity Assets

| Domain | Convention | Example |
|---|---|---|
| Scenes | PascalCase.unity | `MainMenu.unity` |
| Prefabs | PascalCase | `Player_Sporeling.prefab` |
| Shaders | PascalCase.shadergraph | `InfectionPulse.shadergraph` |
| Materials | PascalCase_MAT | `Sporeling_MAT` |

### Backend

| Domain | Convention | Example |
|---|---|---|
| Supabase tables | snake_case | `skill_tree_nodes` |
| Redis keys | entity:{id}:field | `session:abc:infection:p1` |

---

## Agent Routing Table

When given a task, identify the task type and read the corresponding bible section(s) BEFORE writing any code.

| Task Type | Read These Bible Sections | Key Files |
|---|---|---|
| **ECS / Simulation** | `13-tech-architecture/TECH_ARCH.md` | `Core/ECS/`, `Core/Math/` |
| **Combat / Damage** | `04-combat/COMBAT.md` + `13-tech-architecture/` | `Core/Systems/CombatSystem.cs` |
| **Enemy / AI** | `08-ai-enemies/AI_ENEMIES.md` + `04-combat/` | `Core/Systems/`, `Data/EnemyDatabase.cs` |
| **Networking / Rollback** | `07-multiplayer/MULTIPLAYER.md` + `14-networking/NETCODE.md` | `Core/Netcode/` |
| **Fusion Mechanics** | `07-multiplayer/MULTIPLAYER.md` + `05-classes/CLASSES.md` | `Core/Systems/FusionStateMachine.cs` |
| **Player Classes** | `05-classes/CLASSES.md` + `04-combat/` | `Core/Systems/`, ScriptableObjects |
| **Progression / Boons** | `06-progression/PROGRESSION.md` | `Data/BoonDatabase.cs` |
| **World / Zones** | `03-world/WORLD.md` + `15-procgen/` | `World/`, `ProcGen/` |
| **Proc-Gen (Rust)** | `15-procgen/` + `13-tech-architecture/` | `rust/crates/`, `ProcGen/ProcGenBridge.cs` |
| **Audio / FMOD** | `12-audio/AUDIO.md` | `Audio/AudioManager.cs` |
| **VOIP / Infection DSP** | `12-audio/AUDIO.md` + `04-combat/` | `Audio/SporeTalkDSP.cs` |
| **Narrative / Narrator** | `09-narrative/NARRATIVE.md` + `12-audio/` | `Audio/NarratorManager.cs` |
| **UI / HUD** | `11-ui-ux/UI_UX.md` | `UI/` |
| **Art / Shaders** | `10-art-direction/ART_DIRECTION.md` | `Art/Shaders/` |
| **Backend / Database** | `24-data-schemas/` + `06-progression/` | `backend/`, `Network/BackendClient.cs` |
| **Testing** | `17-testing/` | `Tests/EditMode/`, `Tests/PlayMode/` |
| **Performance** | `18-performance/` + `13-tech-architecture/` | Profiling, budgets |
| **Build / Deploy** | `19-production/` | ProjectSettings, build scripts |
| **Accessibility** | `22-accessibility/` + `11-ui-ux/` | UI, input remapping |

### Routing Logic

```
1. Parse the task description
2. Match to one or more task types from the table above
3. Read ALL listed bible sections for those task types
4. Check docs/eots-bible-unity/29-dependency-map/DEPENDENCIES.md for build order
5. Verify prerequisites are built before starting dependent work
6. Write code, tests, and documentation
7. State what should be built next per the dependency map
```

---

## Prompt Templates

Use these XML-formatted templates when performing common operations. Fill in the placeholders and follow the structure exactly.

### Create ECS Component

```xml
<task type="create-component">
  <bible>Read 13-tech-architecture/TECH_ARCH.md for component conventions</bible>
  <constraints>
    - Must be a struct, not a class
    - Must use FixedPoint64 for all numeric fields (no float, no double, no int for game values)
    - Must be in namespace EotS.Core.ECS
    - Must live in Assets/_Project/Scripts/Core/ECS/Components/
    - Must have no Unity API dependencies (no using UnityEngine)
    - Must implement a Serialize(BinaryWriter) and Deserialize(BinaryReader) method for snapshots
    - Must be <= 64 bytes for cache efficiency
  </constraints>
  <template>
    File: Assets/_Project/Scripts/Core/ECS/Components/{NAME}Component.cs
    Namespace: EotS.Core.ECS
    Type: public struct {NAME}Component
    Fields: FixedPoint64 or FixedVec2 types only
    Methods: Serialize(), Deserialize(), ToString()
  </template>
  <test>
    File: Tests/EditMode/{NAME}ComponentTests.cs
    - Test serialization roundtrip (serialize -> deserialize -> assert equal)
    - Test default values
    - Test boundary values
  </test>
</task>
```

### Create ECS System

```xml
<task type="create-system">
  <bible>Read the relevant gameplay bible section + 13-tech-architecture/TECH_ARCH.md</bible>
  <constraints>
    - Must be a class implementing IEotSSystem (custom interface, not Unity ISystem)
    - Must operate only on component structs via ComponentStorage
    - Must use FixedPoint64 math exclusively (no float, no double)
    - Must be in namespace EotS.Core.Systems
    - Must live in Assets/_Project/Scripts/Core/Systems/
    - Must have no Unity API dependencies
    - Must be deterministic: same input state -> same output state
    - Must be registered in SystemScheduler with explicit execution order
  </constraints>
  <template>
    File: Assets/_Project/Scripts/Core/Systems/{NAME}System.cs
    Namespace: EotS.Core.Systems
    Interface: IEotSSystem
    Method: void Execute(EotSWorld world, FixedPoint64 deltaTime)
    Query: Iterate entities with required components via world.Query()
  </template>
  <test>
    File: Tests/EditMode/{NAME}SystemTests.cs
    - Test system produces correct output for known input
    - Test determinism: run twice with same state, assert identical results
    - Test edge cases: zero entities, max entities, boundary values
  </test>
</task>
```

### Add Enemy Type

```xml
<task type="add-enemy">
  <bible>Read 08-ai-enemies/AI_ENEMIES.md + 04-combat/COMBAT.md</bible>
  <steps>
    1. Define enemy stats as a ScriptableObject entry in EnemyDatabase
    2. Create enemy-specific components if needed (follow create-component template)
    3. Add behavior logic to AI system (follow create-system template if new system needed)
    4. Create prefab: Assets/_Project/Prefabs/Enemies/{NAME}.prefab
    5. Wire up RenderProxy on prefab for ECS -> Unity rendering
    6. Add spawn rules to RoomSpawner configuration
    7. Write tests: AI behavior, damage interaction, death handling
  </steps>
</task>
```

### Add Boon (Echo Boon)

```xml
<task type="add-boon">
  <bible>Read 06-progression/PROGRESSION.md + 04-combat/COMBAT.md</bible>
  <steps>
    1. Define boon data as ScriptableObject: Assets/_Project/ScriptableObjects/Boons/{NAME}.asset
    2. Register in BoonDatabase
    3. Implement boon effect in the relevant system (combat, movement, infection, etc.)
    4. All stat modifications use FixedPoint64
    5. If boon affects fusion: update FusionStateMachine interaction table
    6. Add UI entry for BoonSelectUI
    7. Write tests: effect application, stacking rules, fusion interaction
  </steps>
</task>
```

### Create Bridge Component (Unity <-> ECS)

```xml
<task type="create-bridge">
  <bible>Read 13-tech-architecture/TECH_ARCH.md, Bridge Layer Design section</bible>
  <constraints>
    - Must be a MonoBehaviour (this is the ONLY place Unity API is allowed)
    - Must live in Assets/_Project/Scripts/Bridge/
    - Must be in namespace EotS.Bridge
    - This is the float boundary: convert FixedPoint64 -> float HERE and nowhere else
    - Must hold an ECS entity ID for bidirectional lookup
    - Must not contain game logic -- only data transfer between ECS and Unity
  </constraints>
  <template>
    File: Assets/_Project/Scripts/Bridge/{NAME}Bridge.cs
    Namespace: EotS.Bridge
    Base: MonoBehaviour
    Pattern: Read from EotSWorld in Update/LateUpdate, apply to Unity components
  </template>
</task>
```

### Add Network Message

```xml
<task type="add-netmsg">
  <bible>Read 14-networking/NETCODE.md + 07-multiplayer/MULTIPLAYER.md</bible>
  <constraints>
    - Must be a struct with Serialize/Deserialize methods
    - Must live in Core/Netcode/ (pure C#, no Unity deps)
    - Must use FixedPoint64 for all game values
    - Must fit within MTU (< 1200 bytes payload)
    - Must have a unique message type ID
    - Must handle both reliable and unreliable channels as appropriate
  </constraints>
</task>
```

### Add Audio Event

```xml
<task type="add-audio">
  <bible>Read 12-audio/AUDIO.md</bible>
  <steps>
    1. Define FMOD event path in AudioManager constants
    2. Add trigger point in the relevant system or Bridge component
    3. If positional: attach to entity via AudioManager.PlayAtPosition()
    4. If infection-dependent: route through SporeTalkDSP parameter chain
    5. If narrator: register trigger in NarratorManager event table
  </steps>
</task>
```

---

## Build Order Phases

Development follows a strict dependency chain. Check `docs/eots-bible-unity/29-dependency-map/DEPENDENCIES.md` before starting any work.

### Phase 1 -- Foundation (Parallel Tracks)

```
Track A: Custom ECS + Math (Pure C#)
  1.1  Unity project setup (URP 2D, Input System, folder structure)
  1.2  FixedPoint64.cs + FixedVec2.cs + FixedMath.cs + FixedRandom.cs
  1.3  EotSWorld.cs, EotSEntity.cs, ComponentStorage.cs, SystemScheduler.cs
  1.4  All Component structs (Transform, Velocity, Health, Infection, etc.)
  1.5  Bridge: SimulationRunner.cs, RenderProxy.cs, InputBridge.cs, EntitySpawner.cs

Track B: Backend
  1.6  Supabase schema (players, servers, skill_tree_nodes, contributions)
  1.7  Redis session layer (Lua scripts, session service)

Track C: Proc-Gen (Rust)
  1.8  Rust crate structure, types, RNG (xoshiro256**)
  1.9  Zone graph generator + validator
```

### Phase 2 -- Netcode (Depends on Track A complete)

```
  2.1  Snapshot.cs, SnapshotRingBuffer.cs (needs ECS serialization)
  2.2  RollbackManager.cs, InputBuffer.cs, NetTransport.cs
  2.3  FusionStateMachine.cs, FusionInputRouter.cs
  2.4  MovementSystem.cs, GravitySystem.cs, CollisionSystem.cs
```

### Phase 3 -- Gameplay (Depends on Phase 2 + Track C)

```
  3.1  ProcGenBridge.cs ([DllImport] wrapper for Rust FFI)
  3.2  CombatSystem.cs, InfectionSystem.cs, BoonSystem.cs
  3.3  Fused class abilities + SporePlaySystem.cs
  3.4  RoomSpawner.cs + TilemapBuilder.cs
```

### Phase 4 -- Audio (Parallel to Phases 2-3)

```
  4.1  FMOD for Unity integration + AudioManager.cs
  4.2  VOIP transport (Opus via native plugin, C# wrapper)
  4.3  SporeTalkDSP.cs (infection voice processing chain)
  4.4  NarratorManager.cs (trigger system + FMOD events)
```

### Phase 5 -- Integration

```
  5.1  Room spawning end-to-end (Rust -> ProcGenBridge -> TilemapBuilder -> scene)
  5.2  Full delve loop (matchmaking -> session -> delve -> extraction -> hub)
  5.3  Community Skill Tree integration (Supabase -> Redis -> Unity UI)
  5.4  Rite of Echoes (scheduled PvP mode)
```

### Dependency Rules

- Never start a Phase N system before its Phase N-1 dependencies are complete and tested
- Track A must be complete before Phase 2 begins
- Tracks B and C can proceed in parallel with Track A
- Phase 4 (Audio) has no dependencies on Phases 2-3 except VOIP (which needs netcode)

---

## Quality Gates

Every piece of code must pass these gates before being considered complete.

### Code Quality

- [ ] Every ECS system has corresponding EditMode tests
- [ ] Every ECS component is a `struct` with `Serialize`/`Deserialize` methods
- [ ] No `float` or `double` in any file under `Core/` -- FixedPoint64 only
- [ ] No `using UnityEngine;` in any file under `Core/` -- pure C#
- [ ] All systems are deterministic: same input produces same output
- [ ] No heap allocation in hot paths (ECS tick, snapshot serialize)
- [ ] All code compiles without warnings
- [ ] Naming conventions followed (see table above)

### Architecture Quality

- [ ] Bridge layer is the only place FixedPoint64 -> float conversion occurs
- [ ] No game logic in Bridge/ -- only data transfer
- [ ] No Unity API calls in Core/ -- pure C#, extractable to standalone .NET
- [ ] ScriptableObjects used for data definition, not runtime logic
- [ ] Assembly definitions (.asmdef) enforce dependency boundaries

### Performance Quality

- [ ] Snapshot serialize: < 500 us for 500 entities (measured, not estimated)
- [ ] ECS tick: < 8 ms for full 4096-entity room at 60Hz
- [ ] Room generation: < 100 ms
- [ ] No GC allocation during gameplay tick (struct-only ECS)

### Test Quality

- [ ] EditMode tests for all Core/ code (pure C#, no Play mode needed)
- [ ] PlayMode tests for all Bridge/ integration
- [ ] Determinism test: run simulation N ticks, serialize, run again, compare byte-for-byte
- [ ] Rollback test: inject desync, verify resimulation produces correct state

---

## Project Structure

```
echoes_of_the_spore/
├── CLAUDE.md                                    # THIS FILE -- agent orchestrator
├── README.md                                    # Project overview
├── docs/
│   └── eots-bible-unity/                        # Game Bible (30 sections, read-only)
│       ├── README.md                            # MASTER INDEX -- read this first
│       ├── 00-executive/VISION.md               # Product thesis, success criteria
│       ├── 01-market/MARKET.md                  # Market analysis
│       ├── 02-core-loop/CORE_LOOP.md            # Session flow, run structure
│       ├── 03-world/WORLD.md                    # Biomes, zones, Canopy hub
│       ├── 04-combat/COMBAT.md                  # Damage model, infection scaling
│       ├── 05-classes/CLASSES.md                # Player classes, fusion combos
│       ├── 06-progression/PROGRESSION.md        # Skill trees, boons, economy
│       ├── 07-multiplayer/MULTIPLAYER.md        # Fusion, co-op, Rite of Echoes
│       ├── 08-ai-enemies/AI_ENEMIES.md          # Enemy types, behavior trees
│       ├── 09-narrative/NARRATIVE.md            # Lore, narrator triggers
│       ├── 10-art-direction/ART_DIRECTION.md    # Visual style, sprite specs
│       ├── 11-ui-ux/UI_UX.md                    # HUD, menus, a11y
│       ├── 12-audio/AUDIO.md                    # FMOD, DSP, music layers
│       ├── 13-tech-architecture/TECH_ARCH.md    # Engine, stack, architecture
│       ├── 14-networking/NETCODE.md             # Rollback, snapshots, transport
│       ├── 15-procgen/                          # Rust proc-gen algorithm
│       ├── 16-tooling/                          # Editor tools, debug overlays
│       ├── 17-testing/                          # Test strategy
│       ├── 18-performance/                      # Frame budgets
│       ├── 19-production/                       # Milestones, timeline
│       ├── 20-risk/                             # Risk register
│       ├── 21-monetization/                     # Pricing, DLC
│       ├── 22-accessibility/                    # A11y, CVAA
│       ├── 23-live-ops/                         # Post-launch ops
│       ├── 24-data-schemas/                     # Supabase + Redis schemas
│       ├── 25-prompt-library/                   # Generative art prompts
│       ├── 26-asset-pipeline/                   # Import settings
│       ├── 27-decision-log/                     # ADRs
│       ├── 28-open-questions/                   # Unresolved design
│       ├── 29-dependency-map/DEPENDENCIES.md    # BUILD ORDER -- critical path
│       └── 30-changelog/                        # Bible version history
│
├── eots/                                        # UNITY PROJECT ROOT
│   ├── Assets/
│   │   ├── _Project/
│   │   │   ├── Scripts/
│   │   │   │   ├── Core/                        # PURE C# -- NO UNITY API
│   │   │   │   │   ├── ECS/
│   │   │   │   │   │   ├── EotSWorld.cs         # World container, entity manager
│   │   │   │   │   │   ├── EotSEntity.cs        # Entity ID + component mask
│   │   │   │   │   │   ├── ComponentStorage.cs  # SoA storage per component type
│   │   │   │   │   │   ├── SystemScheduler.cs   # Ordered system execution
│   │   │   │   │   │   └── Components/
│   │   │   │   │   │       ├── TransformComponent.cs
│   │   │   │   │   │       ├── VelocityComponent.cs
│   │   │   │   │   │       ├── HealthComponent.cs
│   │   │   │   │   │       ├── InfectionComponent.cs
│   │   │   │   │   │       ├── PlayerInputComponent.cs
│   │   │   │   │   │       ├── FusionComponent.cs
│   │   │   │   │   │       └── ColliderComponent.cs
│   │   │   │   │   ├── Math/
│   │   │   │   │   │   ├── FixedPoint64.cs      # 32.32 fixed-point (long)
│   │   │   │   │   │   ├── FixedVec2.cs         # 2D vector (FixedPoint64)
│   │   │   │   │   │   ├── FixedMath.cs         # Sin/cos/atan2 lookup tables
│   │   │   │   │   │   └── FixedRandom.cs       # xoshiro256** PRNG
│   │   │   │   │   ├── Netcode/
│   │   │   │   │   │   ├── Snapshot.cs          # Full world state capture
│   │   │   │   │   │   ├── SnapshotRingBuffer.cs
│   │   │   │   │   │   ├── RollbackManager.cs   # Prediction + resimulation
│   │   │   │   │   │   ├── InputBuffer.cs       # Per-player input history
│   │   │   │   │   │   └── NetTransport.cs      # UDP abstraction
│   │   │   │   │   └── Systems/
│   │   │   │   │       ├── MovementSystem.cs
│   │   │   │   │       ├── GravitySystem.cs
│   │   │   │   │       ├── CollisionSystem.cs
│   │   │   │   │       ├── CombatSystem.cs
│   │   │   │   │       ├── InfectionSystem.cs
│   │   │   │   │       ├── FusionStateMachine.cs
│   │   │   │   │       └── FusionInputRouter.cs
│   │   │   │   │
│   │   │   │   ├── Bridge/                      # FLOAT BOUNDARY -- Unity <-> ECS
│   │   │   │   │   ├── SimulationRunner.cs      # Ticks ECS at 60Hz via FixedUpdate
│   │   │   │   │   ├── RenderProxy.cs           # ECS state -> SpriteRenderer
│   │   │   │   │   ├── InputBridge.cs           # Unity Input System -> ECS
│   │   │   │   │   └── EntitySpawner.cs         # ECS entity events -> prefabs
│   │   │   │   │
│   │   │   │   ├── ProcGen/
│   │   │   │   │   ├── ProcGenBridge.cs         # [DllImport] Rust FFI wrapper
│   │   │   │   │   └── ZoneGraphData.cs         # C# structs mirroring Rust
│   │   │   │   │
│   │   │   │   ├── Audio/
│   │   │   │   │   ├── AudioManager.cs          # FMOD event orchestration
│   │   │   │   │   ├── NarratorManager.cs       # Contextual narrator triggers
│   │   │   │   │   └── SporeTalkDSP.cs          # Infection voice processing
│   │   │   │   │
│   │   │   │   ├── UI/
│   │   │   │   │   ├── HUDController.cs
│   │   │   │   │   ├── InfectionDisplay.cs
│   │   │   │   │   ├── BoonSelectUI.cs
│   │   │   │   │   └── CommunityTreeUI.cs
│   │   │   │   │
│   │   │   │   ├── World/
│   │   │   │   │   ├── RoomSpawner.cs           # Instantiate rooms from graph
│   │   │   │   │   ├── TilemapBuilder.cs        # Proc-gen -> Unity Tilemap
│   │   │   │   │   └── CanopyManager.cs         # Hub world evolution
│   │   │   │   │
│   │   │   │   ├── Network/
│   │   │   │   │   ├── NetworkManager.cs        # High-level session management
│   │   │   │   │   ├── MatchmakingService.cs    # Server discovery, lobby
│   │   │   │   │   └── BackendClient.cs         # HTTP -> Supabase + Redis
│   │   │   │   │
│   │   │   │   └── Data/
│   │   │   │       ├── BoonDatabase.cs          # ScriptableObject registry
│   │   │   │       ├── EnemyDatabase.cs
│   │   │   │       └── ZoneConfig.cs
│   │   │   │
│   │   │   ├── Scenes/
│   │   │   │   ├── MainMenu.unity
│   │   │   │   ├── CanopyHub.unity              # Shared hub world
│   │   │   │   ├── Delve.unity                  # Run scene (rooms loaded additively)
│   │   │   │   ├── RiteArena.unity              # Scheduled PvP
│   │   │   │   └── Test/ProofOfLife.unity       # Minimal test scene
│   │   │   │
│   │   │   ├── Art/
│   │   │   │   ├── Sprites/
│   │   │   │   ├── Tilesets/
│   │   │   │   ├── Parallax/
│   │   │   │   ├── UI/
│   │   │   │   └── Shaders/                     # URP Shader Graph + HLSL
│   │   │   │       ├── InfectionPulse.shadergraph
│   │   │   │       ├── FossilMuralReveal.shadergraph
│   │   │   │       └── SporeParticle.shadergraph
│   │   │   │
│   │   │   ├── Audio/FMODBanks/
│   │   │   ├── Prefabs/
│   │   │   │   ├── Player/
│   │   │   │   ├── Enemies/
│   │   │   │   ├── Rooms/
│   │   │   │   └── UI/
│   │   │   ├── ScriptableObjects/
│   │   │   │   ├── Boons/
│   │   │   │   ├── Enemies/
│   │   │   │   ├── Zones/
│   │   │   │   └── Functions/
│   │   │   └── Fonts/
│   │   │
│   │   ├── Plugins/
│   │   │   ├── FMOD/                            # FMOD for Unity plugin
│   │   │   └── Native/
│   │   │       └── x86_64/
│   │   │           ├── eots_procgen.dll          # Rust (Windows)
│   │   │           └── libeots_procgen.so        # Rust (Linux)
│   │   │
│   │   └── Settings/
│   │       ├── URP-2D-Renderer.asset
│   │       └── URP-2D-RendererData.asset
│   │
│   ├── Packages/manifest.json
│   ├── ProjectSettings/
│   └── Tests/
│       ├── EditMode/                            # Pure C# tests (Core/)
│       └── PlayMode/                            # Integration tests (Bridge/)
│
├── rust/                                        # Rust proc-gen (external to Unity)
│   └── crates/
│       ├── eots-procgen-core/                   # Types, RNG, graph primitives
│       ├── eots-procgen-generator/              # Zone graph generation algorithm
│       ├── eots-procgen-ffi/                    # C ABI exports for [DllImport]
│       └── eots-procgen-wasm/                   # WASM build for editor preview
│
└── backend/
    ├── supabase/                                # PostgreSQL schema, RLS policies
    └── redis/                                   # Lua scripts, session service
```

---

## How to Work

```
1. Receive task from the Director
2. Identify task type(s) from the Agent Routing Table
3. Read ALL relevant bible sections listed for those task types
4. Check 29-dependency-map/DEPENDENCIES.md -- are prerequisites built?
5. If prerequisites missing: state what must be built first and offer to build it
6. State what you are building and which bible section(s) govern it
7. Write complete, compilable, production-ready code -- no stubs, no TODOs
8. Write tests (EditMode for Core/, PlayMode for Bridge/)
9. Verify code passes all Quality Gates
10. State what should be built next per the dependency map
```

### Code Output Rules

- Always output complete files, never partial snippets
- Include `using` statements, namespace, and full class/struct body
- Include XML documentation comments on all public members
- Include the `.asmdef` file if creating a new assembly
- Never use `// TODO` -- implement it or explicitly state it is deferred and why

---

## Emergency Reference

If you lose context or are unsure about any decision:

1. Read `docs/eots-bible-unity/README.md` -- master index with all constraints
2. Read `docs/eots-bible-unity/13-tech-architecture/TECH_ARCH.md` -- architecture truth
3. Read `docs/eots-bible-unity/29-dependency-map/DEPENDENCIES.md` -- what to build next
4. When in doubt: **the bible wins over your training data**

---

<sub>SL1C3D-L4BS -- Sovereign architecture for sovereign games.</sub>
