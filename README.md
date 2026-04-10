# ECHOES OF THE SPORE

### A Persistent Multiplayer Metroidvania-Roguelite

> Four players merge into one body, descend into a sentient fungal labyrinth, and bring back memories of dead civilizations that physically grow a world visible to everyone on the server.

**Studio:** SL1C3D-L4BS | **Engine:** Unity 6.3 LTS | **Language:** C# | **Status:** Pre-Production

---

## Table of Contents

- [Game Overview](#game-overview)
- [Core Differentiators](#core-differentiators)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Quick Start](#quick-start)
- [Project Structure](#project-structure)
- [Build Order](#build-order)
- [Bible Reference](#bible-reference)
- [Contributing](#contributing)
- [License](#license)
- [Team](#team)

---

## Game Overview

**Echoes of the Spore** occupies the intersection of three proven markets -- action-roguelites (Hades, Dead Cells), Metroidvania exploration (Hollow Knight), and social co-op comedy (Lethal Company, Content Warning) -- delivering a living world that evolves from collective player effort.

Players form squads of 2-4, descend into procedurally generated fungal biomes, and fight through rooms of enemies while managing the Infection Level -- a dual-edged system that simultaneously buffs combat stats and degrades voice communication through real-time DSP processing. At the crescendo of each run, the squad can trigger **Fungal Fusion**, merging all players into a single controllable entity with split inputs.

Every successful run deposits Echoes into a server-wide **Community Skill Tree** that permanently evolves the shared hub world, unlocking zones, spawning NPCs, and growing structures visible to all 10,000 players on the server.

**Price:** $29.99 premium. No live-service treadmill. No royalties. No runtime fees.

---

## Core Differentiators

### 1. Fungal Fusion

2-4 players physically merge into a single controllable entity. One player controls movement, another controls aim, a third triggers abilities. No shipped game does this. The Fusion State Machine manages the merge sequence, input routing, and the combined stat profile from all contributors.

### 2. Infection-as-Comedy

The Infection Level system creates emergent humor that is inherently viral and clippable. As infection rises, players gain combat power but their voice comms degrade through a real-time DSP chain -- pitch shifting, granular stuttering, reverb spiraling, spectral smearing. By Tier 5, you sound like a sentient mushroom trying to call in an airstrike. FMOD drives the audio pipeline with Opus-aligned 20ms DSP frames.

### 3. Persistent Communal World

Every run contributes to a server-wide Community Skill Tree backed by Supabase (PostgreSQL) and Redis. The shared hub world (The Canopy) physically transforms based on collective progress -- new biomes open, NPCs appear, architecture grows. The world changes whether you are playing or not. 10,000 players per server, 30%+ target DAU/MAU.

---

## Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| **Engine** | Unity 6.3 LTS | Rendering, input, platform export, scene management |
| **Render Pipeline** | URP (2D Renderer) | Sprite rendering, 2D lighting, Shader Graph |
| **Primary Language** | C# (.NET / Unity CoreCLR) | All game code |
| **Simulation ECS** | Custom C# ECS (NOT Unity DOTS) | Deterministic gameplay simulation |
| **Math** | FixedPoint64 (32.32 int64) | Cross-platform deterministic arithmetic |
| **Netcode** | Custom Rollback (Pure C#) | Input prediction, snapshot serialization, resimulation |
| **Proc-Gen** | Rust (no_std) via C FFI / [DllImport] | Zone graph generation, room layouts |
| **Audio** | FMOD for Unity | Sound design, music, DSP chain |
| **VOIP** | Opus codec + C# DSP | Voice comms with infection degradation |
| **Backend** | Supabase (PostgreSQL) + Redis | Community Tree, sessions, matchmaking |
| **Input** | Unity Input System | Action-based, rebindable controls |
| **Camera** | Cinemachine 3.x | 2D follow, screen shake, fusion zoom |
| **Analytics** | Unity Analytics + Mixpanel | Telemetry, A/B testing |
| **Testing** | Unity Test Framework | EditMode (pure C#) + PlayMode (integration) |

### Why Custom ECS Instead of Unity DOTS?

Unity DOTS uses float math internally and is not deterministic across platforms. Our rollback netcode requires bit-identical simulation on every client. The custom ECS is approximately 2,000 lines of pure C# with zero Unity API dependencies, uses FixedPoint64 exclusively, and can be extracted to a standalone .NET console app for dedicated servers.

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                       UNITY 6.3 LTS                             │
│                                                                 │
│  ┌──────────────┐  ┌───────────────┐  ┌──────────────────────┐  │
│  │  URP 2D      │  │ Input System  │  │  FMOD for Unity      │  │
│  │  SpriteRend  │  │ Actions +     │  │  Banks + DSP Events  │  │
│  │  Tilemap     │  │ Rebinding     │  │  Opus VOIP           │  │
│  │  Cinemachine │  │               │  │  SporeTalkDSP        │  │
│  └──────┬───────┘  └──────┬────────┘  └──────────┬───────────┘  │
│         │                 │                      │              │
│  ┌──────▼─────────────────▼──────────────────────▼───────────┐  │
│  │              BRIDGE LAYER (MonoBehaviours)                 │  │
│  │  SimulationRunner  RenderProxy  InputBridge  EntitySpawner │  │
│  │  AudioManager  NarratorManager  NetworkManager             │  │
│  │  -- Float boundary: FixedPoint64 → float ONLY here --     │  │
│  └──────────────────────────┬────────────────────────────────┘  │
│                             │ (C# method calls, no Unity API)   │
│  ┌──────────────────────────▼────────────────────────────────┐  │
│  │          CUSTOM ECS (Pure C#, Zero Unity Dependencies)     │  │
│  │  EotSWorld  EotSEntity  ComponentStorage  SystemScheduler  │  │
│  │  FixedPoint64  FixedVec2  FixedMath  FixedRandom           │  │
│  │  MovementSystem  GravitySystem  CollisionSystem            │  │
│  │  CombatSystem  InfectionSystem  FusionStateMachine         │  │
│  │  -- 60Hz deterministic tick, all int64 math --             │  │
│  └──────────────────────────┬────────────────────────────────┘  │
│                             │                                   │
│  ┌──────────────────────────▼────────────────────────────────┐  │
│  │            ROLLBACK NETCODE (Pure C#)                      │  │
│  │  Snapshot  SnapshotRingBuffer  RollbackManager             │  │
│  │  InputBuffer  NetTransport  FusionInputRouter              │  │
│  │  -- 10-tick window, <500us serialize, <50ms RTT --         │  │
│  └──────────────────────────┬────────────────────────────────┘  │
│                             │ (UDP via System.Net.Sockets)      │
└─────────────────────────────┼──────────────────────────────────┘
                              │
        ┌─────────────────────▼──────────────────────┐
        │          DEDICATED SERVER                   │
        │  Unity Headless Build (Option A)            │
        │  or Standalone .NET Console (Option B)      │
        │                                             │
        │  ┌───────────┐  ┌────────────────────────┐  │
        │  │ Supabase  │  │ Redis Session Layer    │  │
        │  │ PostgreSQL│  │ Lua Scripts            │  │
        │  │ REST API  │  │ Matchmaking + Sessions │  │
        │  └───────────┘  └────────────────────────┘  │
        └─────────────────────────────────────────────┘

        ┌─────────────────────────────────────────────┐
        │          RUST PROC-GEN (Native Plugin)      │
        │  eots-procgen-core     (types, RNG)         │
        │  eots-procgen-generator (zone graphs)       │
        │  eots-procgen-ffi      (C ABI exports)      │
        │  Loaded via [DllImport] in ProcGenBridge.cs  │
        │  -- no_std, xoshiro256**, <100ms room gen -- │
        └─────────────────────────────────────────────┘
```

---

## Quick Start

### Prerequisites

| Tool | Version | Notes |
|---|---|---|
| Unity Hub | Latest | Install Unity 6.3 LTS via Hub |
| Unity | 6.3.x LTS | URP, Input System, Cinemachine packages |
| .NET SDK | 8.0+ | For standalone test runner |
| Rust | 1.78+ | For proc-gen native plugin |
| FMOD Studio | 2.03+ | Audio authoring |
| Visual Studio 2022 | Community+ | C# + Unity workload |
| Git LFS | 3.0+ | Binary asset tracking |

### Setup

```bash
# Clone the repository
git clone https://github.com/SL1C3D-L4BS/echoes-of-the-spore.git
cd echoes-of-the-spore

# Build the Rust proc-gen native plugin
cd rust
cargo build --release -p eots-procgen-ffi
# Copy the built library to Unity Plugins
cp target/release/eots_procgen.dll ../eots/Assets/Plugins/Native/x86_64/  # Windows
cp target/release/libeots_procgen.so ../eots/Assets/Plugins/Native/x86_64/ # Linux

# Open the Unity project
# In Unity Hub: Add project from disk → select eots/
# Unity will import packages and compile scripts

# Run tests
# Unity Editor → Window → General → Test Runner
# Run all EditMode tests first (pure C# ECS validation)
# Run PlayMode tests second (Bridge integration)
```

### First Run Checklist

1. Open `eots/Assets/_Project/Scenes/Test/ProofOfLife.unity`
2. Enter Play mode -- SimulationRunner should tick the ECS at 60Hz
3. Verify the console shows entity count and tick number
4. Open `MainMenu.unity` for the full game flow

---

## Project Structure

```
echoes_of_the_spore/
├── CLAUDE.md                              # Agent orchestrator (Claude Code)
├── README.md                              # This file
├── docs/
│   └── eots-bible-unity/                  # Game Bible (30 sections)
│       ├── README.md                      # Master index, constraints, conventions
│       ├── 00-executive/                  # Vision, product thesis
│       ├── 01-market/                     # Market analysis, comp matrix
│       ├── 02-core-loop/                  # Gameplay loop, session flow
│       ├── 03-world/                      # Biomes, zones, Canopy hub
│       ├── 04-combat/                     # Combat systems, damage model
│       ├── 05-classes/                    # Player classes, fusion combos
│       ├── 06-progression/                # Skill trees, Echo economy
│       ├── 07-multiplayer/                # Fusion mechanics, co-op design
│       ├── 08-ai-enemies/                 # Enemy AI, behavior trees
│       ├── 09-narrative/                  # Lore, narrator system
│       ├── 10-art-direction/              # Visual style, sprite specs
│       ├── 11-ui-ux/                      # HUD, menus, accessibility
│       ├── 12-audio/                      # FMOD design, music system
│       ├── 13-tech-architecture/          # Engine strategy, stack overview
│       ├── 14-networking/                 # Netcode protocol, rollback
│       ├── 15-procgen/                    # Rust proc-gen, zone graphs
│       ├── 16-tooling/                    # Editor tools, debug overlays
│       ├── 17-testing/                    # Test strategy, coverage reqs
│       ├── 18-performance/                # Budgets, profiling targets
│       ├── 19-production/                 # Milestones, team structure
│       ├── 20-risk/                       # Risk register, mitigations
│       ├── 21-monetization/               # Pricing, DLC strategy
│       ├── 22-accessibility/              # A11y features, CVAA compliance
│       ├── 23-live-ops/                   # Post-launch, seasons, events
│       ├── 24-data-schemas/               # Supabase tables, Redis keys
│       ├── 25-prompt-library/             # Generative art prompts
│       ├── 26-asset-pipeline/             # Import settings, naming
│       ├── 27-decision-log/               # Architecture decision records
│       ├── 28-open-questions/             # Unresolved design questions
│       ├── 29-dependency-map/             # Build order, critical path
│       └── 30-changelog/                  # Bible version history
├── eots/                                  # Unity project root
│   ├── Assets/
│   │   ├── _Project/                      # All game-specific assets
│   │   │   ├── Scripts/
│   │   │   │   ├── Core/                  # ZERO Unity dependencies
│   │   │   │   │   ├── ECS/               # Custom deterministic ECS
│   │   │   │   │   │   ├── EotSWorld.cs
│   │   │   │   │   │   ├── EotSEntity.cs
│   │   │   │   │   │   ├── ComponentStorage.cs
│   │   │   │   │   │   ├── SystemScheduler.cs
│   │   │   │   │   │   └── Components/    # All struct, all FixedPoint64
│   │   │   │   │   ├── Math/              # FixedPoint64, FixedVec2, LUTs
│   │   │   │   │   ├── Netcode/           # Rollback, snapshots, transport
│   │   │   │   │   └── Systems/           # Movement, combat, fusion, etc.
│   │   │   │   ├── Bridge/                # Unity <-> ECS adapters (float boundary)
│   │   │   │   ├── ProcGen/               # [DllImport] wrappers for Rust FFI
│   │   │   │   ├── Audio/                 # FMOD integration, SporeTalkDSP
│   │   │   │   ├── UI/                    # HUD, menus, Community Tree UI
│   │   │   │   ├── World/                 # Room spawning, tilemap builder
│   │   │   │   ├── Network/               # High-level networking, matchmaking
│   │   │   │   └── Data/                  # ScriptableObject registries
│   │   │   ├── Scenes/
│   │   │   │   ├── MainMenu.unity
│   │   │   │   ├── CanopyHub.unity
│   │   │   │   ├── Delve.unity
│   │   │   │   ├── RiteArena.unity
│   │   │   │   └── Test/ProofOfLife.unity
│   │   │   ├── Art/                       # Sprites, tilesets, shaders
│   │   │   ├── Audio/FMODBanks/
│   │   │   ├── Prefabs/                   # Player, enemies, rooms, UI
│   │   │   ├── ScriptableObjects/         # Boons, enemies, zones, functions
│   │   │   └── Fonts/
│   │   ├── Plugins/
│   │   │   ├── FMOD/                      # FMOD for Unity plugin
│   │   │   └── Native/x86_64/             # Rust .dll/.so native plugins
│   │   └── Settings/                      # URP renderer assets
│   ├── Packages/manifest.json
│   ├── ProjectSettings/
│   └── Tests/
│       ├── EditMode/                      # Pure C# unit tests (Core/)
│       └── PlayMode/                      # Integration tests (Bridge/)
├── rust/                                  # Rust proc-gen (external to Unity)
│   └── crates/
│       ├── eots-procgen-core/             # Types, RNG, graph primitives
│       ├── eots-procgen-generator/        # Zone graph generation
│       ├── eots-procgen-ffi/              # C ABI exports for Unity
│       └── eots-procgen-wasm/             # WASM build for tools/preview
└── backend/
    ├── supabase/                          # PostgreSQL schema, RLS policies
    └── redis/                             # Lua scripts, session config
```

---

## Build Order

Development follows a strict dependency chain defined in `docs/eots-bible-unity/29-dependency-map/DEPENDENCIES.md`.

### Phase 1 -- Foundation (Parallel Tracks)

Three tracks run simultaneously. No cross-track dependencies.

| Track | Steps | Output |
|---|---|---|
| **A: Custom ECS + Math** | Unity project setup (URP 2D) -> FixedPoint64 + FixedVec2 + FixedMath -> EotSWorld + ComponentStorage + SystemScheduler -> Component structs -> Bridge layer (SimulationRunner, RenderProxy, InputBridge, EntitySpawner) | Deterministic simulation ticking at 60Hz with Unity rendering |
| **B: Backend** | Supabase schema (players, servers, skill_tree_nodes) -> Redis session layer (Lua scripts, session service) | Persistent storage and real-time session infrastructure |
| **C: Proc-Gen** | Rust crate structure + types + RNG -> Zone graph generator + validator | Native plugin producing deterministic zone layouts |

### Phase 2 -- Netcode (Depends on Track A)

| Step | System | Dependency |
|---|---|---|
| 2.1 | Snapshot + SnapshotRingBuffer | ECS serialization |
| 2.2 | RollbackManager + InputBuffer + NetTransport | Snapshots |
| 2.3 | FusionStateMachine + FusionInputRouter | Rollback |
| 2.4 | MovementSystem + GravitySystem + CollisionSystem | ECS systems |

### Phase 3 -- Gameplay (Depends on Phase 2 + Track C)

| Step | System | Dependency |
|---|---|---|
| 3.1 | ProcGenBridge ([DllImport] wrapper) | Rust FFI crate |
| 3.2 | CombatSystem + InfectionSystem + BoonSystem | Character controller |
| 3.3 | Fused class abilities + SporePlaySystem | Fusion + Combat |
| 3.4 | RoomSpawner + TilemapBuilder | Proc-gen bridge + Tilemap |

### Phase 4 -- Audio (Parallel to Phases 2-3)

| Step | System | Dependency |
|---|---|---|
| 4.1 | FMOD for Unity integration + AudioManager | Unity project |
| 4.2 | VOIP transport (Opus via native plugin) | Redis + Netcode |
| 4.3 | SporeTalkDSP (infection voice processing) | VOIP transport |
| 4.4 | NarratorManager (trigger system + FMOD events) | FMOD + Infection |

### Phase 5 -- Integration

Room spawning, full delve loop, Community Tree integration, end-to-end multiplayer sessions.

---

## Bible Reference

The complete game bible lives in `docs/eots-bible-unity/`. It is the single source of truth for all design and engineering decisions.

| Section | Path | Contents |
|---|---|---|
| Executive Vision | `00-executive/VISION.md` | Product thesis, success criteria, player fantasies |
| Market Analysis | `01-market/MARKET.md` | Comp matrix, TAM, positioning |
| Core Loop | `02-core-loop/CORE_LOOP.md` | Session flow, run structure, economy |
| World Design | `03-world/WORLD.md` | Biomes, Canopy hub, zone layout |
| Combat | `04-combat/COMBAT.md` | Damage model, abilities, infection scaling |
| Classes | `05-classes/CLASSES.md` | Player archetypes, fusion combinations |
| Progression | `06-progression/PROGRESSION.md` | Skill trees, Echo economy, Community Tree |
| Multiplayer | `07-multiplayer/MULTIPLAYER.md` | Fusion mechanics, co-op design, Rite of Echoes |
| AI / Enemies | `08-ai-enemies/AI_ENEMIES.md` | Enemy types, behavior trees, boss design |
| Narrative | `09-narrative/NARRATIVE.md` | Lore, narrator system, environmental storytelling |
| Art Direction | `10-art-direction/ART_DIRECTION.md` | Visual style, sprite specs, bioluminescence |
| UI/UX | `11-ui-ux/UI_UX.md` | HUD layout, menu flow, accessibility |
| Audio | `12-audio/AUDIO.md` | FMOD design, music layers, DSP chain |
| Tech Architecture | `13-tech-architecture/TECH_ARCH.md` | Engine strategy, stack overview, invariants |
| Networking | `14-networking/NETCODE.md` | Rollback protocol, snapshot format, transport |
| Proc-Gen | `15-procgen/` | Rust architecture, zone graph algorithm |
| Tooling | `16-tooling/` | Editor tools, debug overlays, replay viewer |
| Testing | `17-testing/` | Test strategy, coverage requirements |
| Performance | `18-performance/` | Frame budgets, profiling targets |
| Production | `19-production/` | Milestones, team structure, timeline |
| Risk | `20-risk/` | Risk register, mitigations |
| Monetization | `21-monetization/` | Pricing, DLC, no MTX commitment |
| Accessibility | `22-accessibility/` | A11y features, CVAA compliance |
| Live Ops | `23-live-ops/` | Post-launch seasons, community events |
| Data Schemas | `24-data-schemas/` | Supabase tables, Redis key patterns |
| Dependency Map | `29-dependency-map/DEPENDENCIES.md` | Build order, critical path, system deps |

---

## Contributing

### Branch Strategy

- `main` -- stable, passes all tests, buildable
- `dev` -- integration branch, CI must pass
- `feature/{section}-{description}` -- feature work (e.g., `feature/ecs-fixed-point-math`)
- `fix/{issue-number}-{description}` -- bug fixes

### Code Standards

- Every system must have corresponding EditMode tests
- Every ECS component must be a `struct` (no classes, no heap allocation)
- No `float` or `double` in any file under `Core/` -- FixedPoint64 only
- No `using UnityEngine;` in any file under `Core/` -- pure C#
- Bridge layer is the single float boundary (FixedPoint64 -> float conversion)
- All code must compile without warnings
- PRs require review from at least one other contributor

### Commit Convention

```
type(scope): description

Types: feat, fix, refactor, test, docs, build, ci
Scopes: ecs, math, netcode, bridge, combat, procgen, audio, ui, backend
```

---

## License

Proprietary. All rights reserved by SL1C3D-L4BS.

This repository and all associated documentation, code, art assets, and design documents are confidential. Unauthorized distribution, reproduction, or use is strictly prohibited.

---

## Team

**SL1C3D-L4BS**

| Role | Name |
|---|---|
| Lead Architect / Director | Michael A. Cooper |

---

<sub>SL1C3D-L4BS -- Built with obsession, shipped with precision.</sub>
