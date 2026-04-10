# 30 — CHANGELOG

## v2026.3 (Unity 6.3 LTS Edition)

### Engine: Unity 6.3 LTS
- Primary language: C# (both simulation and presentation)
- Render pipeline: URP 2D Renderer
- Scripting backend: IL2CPP for builds, Mono in Editor
- Console export: built-in (PS5, Xbox, Switch via platform SDKs)
- MCP connection: verified with Claude Code

### Key Architecture Decisions
- Custom ECS in **pure C#** (not DOTS) for deterministic FixedPoint64 simulation
- FixedPoint64 implemented as C# struct wrapping `long` (int64)
- Bridge layer: SimulationRunner, RenderProxy, InputBridge, EntitySpawner (MonoBehaviours)
- Rust proc-gen loaded via Unity NativePlugin system ([DllImport])
- FMOD for Unity plugin for audio
- ScriptableObjects for all game data (boons, enemies, zones)
- Headless Unity for dedicated server (extractable to standalone .NET)

### What Did NOT Change (Engine-Independent)
- All game design (classes, boons, fusion, infection, zones, bosses)
- All art direction (Brutalist Bioluminescence)
- All narrative (8 civilizations, True Ending, Mycelium Voice)
- All financial projections and monetization
- Supabase + Redis backend architecture
- Rust proc-gen crate (algorithm unchanged, only FFI wrapper differs)
- 60Hz fixed tick, 10-tick rollback, FixedPoint64 determinism

### Unity-Specific Benefits
- ScriptableObject data pipeline: designers edit boons/enemies/zones in Inspector
- Unity Shader Graph for infection pulse, mural reveal, spore particles
- Cinemachine 2D for camera (follow, screen shake, dead zones)
- Unity Test Runner for EditMode (pure C# ECS tests) and PlayMode (integration)
- Addressables for async room loading
- 2D Lights (URP): point lights for bioluminescence, global for ambient
- Post-processing: Bloom (infection glow), Vignette (damage)
- Pixel Perfect Camera for crisp sprite rendering

### Unity License Cost
- Personal: Free (< $200K revenue)
- Pro: $2,200/seat/year (required at our projected revenue)
- 25 seats × 2 years = $110,000 total (1.1% of $10M budget)
- No royalties. No runtime fee. Permanently cancelled.
