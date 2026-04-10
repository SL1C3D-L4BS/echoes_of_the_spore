# Unity Project Structure

```
echoes_of_the_spore/
├── CLAUDE.md                          # Agent orchestrator
├── README.md                          # Project overview
├── .gitignore                         # Unity-standard ignores
│
├── docs/
│   └── eots-bible-unity/              # 31 sections, 10+ docs each
│
├── eots/                              # Unity project root
│   ├── Assets/
│   │   ├── Scripts/
│   │   │   ├── EotS.Core/             # ── PURE C# (zero Unity deps) ──
│   │   │   │   ├── EotS.Core.asmdef
│   │   │   │   ├── ECS/
│   │   │   │   │   ├── EotSWorld.cs
│   │   │   │   │   ├── IComponent.cs
│   │   │   │   │   ├── ISystem.cs
│   │   │   │   │   ├── ComponentStorage.cs
│   │   │   │   │   ├── SystemPipeline.cs
│   │   │   │   │   ├── EventBus.cs
│   │   │   │   │   └── TickContext.cs
│   │   │   │   ├── Components/
│   │   │   │   │   ├── TransformComponent.cs
│   │   │   │   │   ├── VelocityComponent.cs
│   │   │   │   │   ├── HealthComponent.cs
│   │   │   │   │   ├── CollisionComponent.cs
│   │   │   │   │   ├── HitboxComponent.cs
│   │   │   │   │   ├── InputComponent.cs
│   │   │   │   │   ├── InfectionComponent.cs
│   │   │   │   │   ├── StatusEffectComponent.cs
│   │   │   │   │   ├── FusionComponent.cs
│   │   │   │   │   ├── AIComponent.cs
│   │   │   │   │   └── SilkGaugeComponent.cs
│   │   │   │   ├── Systems/
│   │   │   │   │   ├── InputSystem.cs
│   │   │   │   │   ├── MovementSystem.cs
│   │   │   │   │   ├── CollisionSystem.cs
│   │   │   │   │   ├── CombatSystem.cs
│   │   │   │   │   ├── StatusEffectSystem.cs
│   │   │   │   │   ├── InfectionSystem.cs
│   │   │   │   │   ├── FusionSystem.cs
│   │   │   │   │   ├── AISystem.cs
│   │   │   │   │   └── SpawnSystem.cs
│   │   │   │   ├── Math/
│   │   │   │   │   ├── FixedPoint64.cs
│   │   │   │   │   ├── Vector2FP.cs
│   │   │   │   │   ├── AABB.cs
│   │   │   │   │   ├── SpatialHash.cs
│   │   │   │   │   └── Xoshiro256.cs
│   │   │   │   └── Netcode/
│   │   │   │       ├── SnapshotSerializer.cs
│   │   │   │       ├── RollbackManager.cs
│   │   │   │       ├── InputBuffer.cs
│   │   │   │       ├── UDPTransport.cs
│   │   │   │       └── ChecksumValidator.cs
│   │   │   │
│   │   │   ├── EotS.Bridge/           # ── UNITY ↔ ECS ADAPTER ──
│   │   │   │   ├── EotS.Bridge.asmdef
│   │   │   │   ├── SimulationRunner.cs
│   │   │   │   ├── RenderProxy.cs
│   │   │   │   ├── InputBridge.cs
│   │   │   │   ├── EntitySpawner.cs
│   │   │   │   ├── AudioBridge.cs
│   │   │   │   └── CameraBridge.cs
│   │   │   │
│   │   │   ├── EotS.UI/               # ── UI (MonoBehaviour) ──
│   │   │   │   ├── EotS.UI.asmdef
│   │   │   │   ├── HUD/
│   │   │   │   ├── Menus/
│   │   │   │   └── Overlays/
│   │   │   │
│   │   │   ├── EotS.Audio/            # ── FMOD INTEGRATION ──
│   │   │   │   ├── EotS.Audio.asmdef
│   │   │   │   ├── AudioManager.cs
│   │   │   │   ├── SporeTalkDSP.cs
│   │   │   │   └── NarratorManager.cs
│   │   │   │
│   │   │   ├── EotS.ProcGen/          # ── RUST FFI BRIDGE ──
│   │   │   │   ├── EotS.ProcGen.asmdef
│   │   │   │   ├── ProcGenBridge.cs
│   │   │   │   └── ZoneGraphData.cs
│   │   │   │
│   │   │   └── EotS.Backend/          # ── SUPABASE + REDIS ──
│   │   │       ├── EotS.Backend.asmdef
│   │   │       ├── BackendClient.cs
│   │   │       ├── CommunityTreeSync.cs
│   │   │       └── MatchmakingClient.cs
│   │   │
│   │   ├── Scenes/
│   │   │   ├── MainMenu.unity
│   │   │   ├── Canopy.unity           # Hub world
│   │   │   └── Delve.unity            # Procedural run scene
│   │   │
│   │   ├── Prefabs/
│   │   │   ├── Players/
│   │   │   ├── Enemies/
│   │   │   ├── Rooms/
│   │   │   ├── Hazards/
│   │   │   └── VFX/
│   │   │
│   │   ├── Art/
│   │   │   ├── Sprites/
│   │   │   ├── Tilesets/
│   │   │   ├── Animations/
│   │   │   └── UI/
│   │   │
│   │   ├── Shaders/
│   │   │   ├── InfectionPulse.shadergraph
│   │   │   ├── FossilMuralReveal.shadergraph
│   │   │   └── Bioluminescence.shadergraph
│   │   │
│   │   ├── Audio/
│   │   │   ├── FMOD/                  # FMOD Studio project
│   │   │   └── Banks/                 # Built FMOD banks
│   │   │
│   │   ├── ScriptableObjects/
│   │   │   ├── Classes/               # ClassData SO per class
│   │   │   ├── Crests/                # CrestData SO per crest
│   │   │   ├── Boons/                 # BoonData SO per boon
│   │   │   ├── Enemies/               # EnemyData SO per enemy
│   │   │   └── Zones/                 # ZoneData SO per zone
│   │   │
│   │   ├── Plugins/
│   │   │   └── Native/
│   │   │       └── Win64/
│   │   │           └── eots_procgen.dll   # Rust proc-gen
│   │   │
│   │   └── Resources/                 # Minimal, prefer Addressables
│   │
│   ├── Packages/
│   │   └── manifest.json
│   │
│   ├── Tests/
│   │   ├── EditMode/
│   │   │   ├── ECS/
│   │   │   ├── Math/
│   │   │   ├── Netcode/
│   │   │   └── Determinism/
│   │   └── PlayMode/
│   │       ├── Bridge/
│   │       ├── Combat/
│   │       └── Integration/
│   │
│   └── ProjectSettings/
│
└── rust/                              # Rust proc-gen workspace
    ├── Cargo.toml
    └── crates/
        ├── eots-procgen-core/
        ├── eots-procgen-zone/
        └── eots-procgen-ffi/
```

## Assembly Definition Dependencies

```
EotS.Core          → (none, pure C#)
EotS.Bridge        → EotS.Core, UnityEngine
EotS.UI            → EotS.Core, EotS.Bridge, UnityEngine, UnityEngine.UI, TMPro
EotS.Audio         → EotS.Core, EotS.Bridge, UnityEngine, FMODUnity
EotS.ProcGen       → EotS.Core, UnityEngine
EotS.Backend       → EotS.Core, UnityEngine
Tests.EditMode     → EotS.Core (test Core in isolation)
Tests.PlayMode     → EotS.Core, EotS.Bridge (test integration)
```
