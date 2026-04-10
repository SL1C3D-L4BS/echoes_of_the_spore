# 13 — TECHNICAL ARCHITECTURE & ENGINE STRATEGY

## Engine: Unity 6.3 LTS

### Why Unity

| Factor | Unity 6.3 LTS | Godot 4.6 | Unreal 5.5 |
|---|---|---|---|
| License cost | Free < $200K; Pro $2,200/yr; no royalties | $0 (MIT) | 5% royalty after $1M |
| Runtime fee | Cancelled permanently | N/A | N/A |
| 2D pipeline maturity | Excellent (SpriteRenderer, Tilemap, URP 2D) | Native, good | Poor (3D engine) |
| Primary language | C# (Claude Code excellent) | GDScript / C# | C++ (complex) |
| Console export | Built-in (PS5, Xbox, Switch) | Requires W4 Games | Built-in |
| MCP tool support | Verified working with Claude Code | Verified working | Not verified |
| 2D games shipped | Hollow Knight, Silksong, Dead Cells, Cuphead | Slay the Spire 2, Dome Keeper | None notable |
| Netcode ecosystem | Netcode for GO, Fish-Net, Mirror, custom | Snopek addon | Custom only |
| Asset Store | Massive (tools, art, plugins) | Growing | Large |
| Deterministic physics | Not built-in (custom required) | SG Physics 2D exists | Custom required |
| DOTS/ECS | Available but not deterministic by default | N/A | N/A |
| Editor platform | Win, Mac, Linux (Ubuntu) | Win, Mac, Linux | Win, Mac, Linux |
| Build times | 2-10 minutes | < 1 minute | 15-20 minutes |

### Key Decision: Custom ECS in C#, NOT Unity DOTS

Unity DOTS (Data-Oriented Technology Stack) provides a high-performance ECS. However:
- DOTS uses float math internally — NOT deterministic across platforms
- DOTS is complex (Burst compiler, Jobs system, managed/unmanaged component split)
- Our ECS needs are modest (< 4,096 entities, 2D only)
- Custom ECS in pure C# with FixedPoint64 is ~2,000 lines and guarantees determinism
- We use Unity ONLY for rendering, input, audio, scene management, and platform export
- The custom ECS lives in `Assets/_Project/Scripts/Core/ECS/` — zero Unity API dependencies in core ECS files

## Stack Overview

| Layer | Technology | Rationale |
|---|---|---|
| Engine | Unity 6.3 LTS | Proven 2D, C#, console export, MCP verified |
| Render Pipeline | URP (2D Renderer) | Lightweight, Shader Graph, 2D lighting |
| Simulation | Custom C# ECS (pure, no Unity deps) | Deterministic, rollback-compatible |
| Math | FixedPoint64 struct (C#, 32.32 int64) | Cross-platform determinism |
| Bridge | MonoBehaviours (SimulationRunner, RenderProxy) | Unity GO ↔ ECS adapter |
| Input | Unity Input System package | Modern, action-based, rebindable |
| Proc-Gen | Rust → C FFI → [DllImport] in C# | Performance, determinism |
| Audio | FMOD for Unity plugin | Industry standard, DSP support |
| VOIP DSP | C# AudioClip processing + native plugin | Infection voice effects |
| Backend | Supabase (PostgreSQL) + Redis | Community Tree, sessions |
| Analytics | Unity Analytics + Mixpanel | Telemetry, A/B testing |

## Architecture Diagram

```
┌───────────────────────────────────────────────────────────┐
│                    UNITY 6.3 LTS                           │
│                                                            │
│  ┌─────────────┐  ┌──────────────┐  ┌─────────────────┐   │
│  │ URP 2D      │  │ Input System │  │ FMOD for Unity  │   │
│  │ SpriteRend  │  │              │  │                 │   │
│  │ Tilemap     │  │              │  │                 │   │
│  │ ParallaxBG  │  │              │  │                 │   │
│  └──────┬──────┘  └──────┬───────┘  └────────┬────────┘   │
│         │                │                   │             │
│  ┌──────▼────────────────▼───────────────────▼──────────┐  │
│  │            BRIDGE LAYER (MonoBehaviours)              │  │
│  │  SimulationRunner | RenderProxy | InputBridge         │  │
│  │  EntitySpawner | AudioManager | NarratorManager       │  │
│  └──────────────────────┬───────────────────────────────┘  │
│                         │ (C# method calls)                │
│  ┌──────────────────────▼───────────────────────────────┐  │
│  │         CUSTOM ECS (Pure C#, zero Unity deps)         │  │
│  │  EotSWorld | Components | Systems | SystemScheduler   │  │
│  │  FixedPoint64 math | 60Hz deterministic tick          │  │
│  │  Fully serializable for rollback snapshots            │  │
│  └──────────────────────┬───────────────────────────────┘  │
│                         │                                  │
│  ┌──────────────────────▼───────────────────────────────┐  │
│  │         ROLLBACK NETCODE (Pure C#)                    │  │
│  │  InputBuffer | SnapshotRing | Prediction | Resim      │  │
│  └──────────────────────┬───────────────────────────────┘  │
│                         │ (UDP via System.Net.Sockets)     │
└─────────────────────────┼─────────────────────────────────┘
                          │
          ┌───────────────▼───────────────┐
          │      DEDICATED SERVER          │
          │  (Unity headless / standalone  │
          │   C# console app with ECS)     │
          │  + HTTP → Supabase             │
          │  + HTTP → Redis session svc    │
          └───────────────────────────────┘
```

## Bridge Layer Design

### SimulationRunner.cs (MonoBehaviour, singleton)
```csharp
// Owns the EotSWorld. Ticks it at fixed 60Hz.
// Accumulates Time.deltaTime, steps ECS when accumulated >= 1/60.
// Stores previous + current state for interpolation.
// Exposes GetInterpolationAlpha() for RenderProxy.
```

### RenderProxy.cs (MonoBehaviour, on each rendered entity)
```csharp
// Holds an ECS entity ID.
// Each Update(): reads TransformComponent from current + previous ECS state.
// Lerps position by interpolation alpha.
// Converts FixedPoint64 → float ONLY here (the single float boundary).
// Updates SpriteRenderer color/material for infection visuals.
```

### InputBridge.cs (MonoBehaviour, singleton)
```csharp
// Reads Unity Input System actions (Move, Aim, Abilities, Fusion, Jump).
// Packs into InputCommand struct (FixedPoint64 vectors + bitfield).
// Writes to local player's PlayerInputComponent in ECS.
// Tags with current simulation tick number.
```

### EntitySpawner.cs (MonoBehaviour, singleton)
```csharp
// Listens for ECS entity create/destroy events.
// On create: Instantiate Unity prefab, attach RenderProxy, set entity ID.
// On destroy: Destroy the Unity GameObject.
// Maintains Dictionary<uint, GameObject> for bidirectional lookup.
```

## Dedicated Server

**Option A: Unity Headless Build (recommended for launch)**
- Build with Server Build option (no rendering, no audio)
- Same C# codebase, ECS runs identically
- Unity handles network transport abstraction

**Option B: Standalone C# Console App (for scale)**
- Extract ECS + Netcode into a separate .NET project (no Unity dependency)
- The custom ECS has zero Unity API calls — this is architecturally possible
- Maximum server density, minimum overhead

**Decision:** Start with Option A. Migrate to Option B if server costs exceed budget.

## Key Invariants

1. NO Unity API calls inside `Core/ECS/` or `Core/Math/` or `Core/Netcode/` folders. These are pure C#.
2. FixedPoint64 uses long (int64) internally. No float/double in simulation.
3. Floats exist ONLY in Bridge/ layer (RenderProxy position lerp) and UI.
4. All ECS components are structs (value types), no class references, no heap alloc.
5. Snapshot serialization of 500 entities < 500 μs.
6. Rollback window: 10 ticks. Beyond: full state resync from server.
7. Rust proc-gen produces identical output for identical seeds on all platforms.
8. C# xoshiro256** PRNG and Rust xoshiro256** produce bit-identical sequences for same seeds.
9. Unity's physics engine is DISABLED. All collision/movement is in custom ECS.

## Unity-Specific Configuration

### Required Packages (via Package Manager)
| Package | Version | Purpose |
|---|---|---|
| Universal RP | 17.x (Unity 6.3) | 2D rendering pipeline |
| Input System | 1.11+ | Modern input with rebinding |
| Cinemachine | 3.x | 2D camera follow, screen shake |
| TextMeshPro | Included | UI text rendering |
| Unity Test Framework | Included | Unit + integration tests |
| Addressables | Optional | Async asset loading for rooms |

### Project Settings
- Physics 2D: Gravity = (0, 0), disabled auto-simulation
- Quality: Single quality level (2D game, no LOD needed)
- Player: Color Space = Linear, API = Vulkan + OpenGLES3 fallback
- Time: Fixed Timestep = 0.01666667 (60Hz) — though we manage our own tick in SimulationRunner
- Script Execution Order: SimulationRunner = -100 (runs before everything else)

### Render Pipeline
- URP with 2D Renderer asset
- Pixel Perfect Camera component on main camera
- Sprite sorting: by Y-axis for depth (standard 2D)
- 2D Lights: point lights for bioluminescence, global light for ambient
- Post-processing: Bloom (for infection glow), Vignette (for damage feedback)
