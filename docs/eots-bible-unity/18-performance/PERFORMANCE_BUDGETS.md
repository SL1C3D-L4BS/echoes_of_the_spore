# Performance Budgets

## Frame Budget (16.67ms at 60fps)

| System | Budget | Measured Target |
|--------|--------|-----------------|
| ECS Tick (all systems) | 4.0ms | < 3.0ms |
| Collision (spatial hash + narrow) | 0.8ms | < 0.5ms |
| Snapshot serialize | 0.5ms | < 0.3ms |
| Rollback resimulate (10 ticks worst case) | 5.0ms | < 4.0ms |
| Rendering (URP 2D) | 4.0ms | < 3.5ms |
| Audio (FMOD) | 1.0ms | < 0.8ms |
| UI (UGUI/TMP) | 0.5ms | < 0.3ms |
| Network I/O | 0.3ms | < 0.2ms |
| **Headroom** | **0.57ms** | — |
| **Total** | **16.67ms** | — |

## Memory Budget

| Category | Budget | Notes |
|----------|--------|-------|
| ECS World | 8 MB | 4096 entities × ~2KB component data |
| Snapshot Ring Buffer | 4 MB | 120 snapshots × ~30KB each |
| Sprite Atlases | 128 MB | All character + enemy + tile sprites |
| FMOD Banks | 128 MB | Music, SFX, voice |
| Tilemaps (active rooms) | 32 MB | 3 rooms loaded (current + adjacent) |
| UI | 16 MB | Canvases, fonts, textures |
| Proc-Gen buffer | 1 MB | Zone graph + room data |
| Network buffers | 2 MB | Send/receive, voice codec |
| **Total** | **~320 MB** | Well within 2GB console minimum |

## CPU Targets by Platform

| Platform | CPU | Target FPS | Max ECS Tick |
|----------|-----|-----------|-------------|
| PC (min spec) | i5-8400 / Ryzen 5 2600 | 60fps | 4.0ms |
| PC (recommended) | i7-10700 / Ryzen 7 3700X | 60fps | 2.0ms |
| PS5 | Zen 2 8-core | 60fps | 3.0ms |
| Xbox Series X | Zen 2 8-core | 60fps | 3.0ms |
| Xbox Series S | Zen 2 8-core (lower clock) | 60fps | 3.5ms |
| Switch (docked) | ARM A57 4-core | 30fps* | 8.0ms |
| Switch (handheld) | ARM A57 4-core | 30fps* | 8.0ms |

*Switch runs simulation at 60Hz internally but renders at 30fps with frame interpolation.

## Network Budget

| Direction | Budget per Player |
|-----------|------------------|
| Upload | 5 KB/s (input packets) |
| Download | 15 KB/s (all inputs + occasional state) |
| Voice | 3 KB/s (Opus 24kbps) |
| Total | ~23 KB/s |

## Loading Times

| Event | Budget | Method |
|-------|--------|--------|
| Initial boot to menu | < 5s | Minimal scene, lazy-load |
| Menu to gameplay | < 3s | Async scene load + proc-gen |
| Room transition | < 0.1s | Pre-loaded adjacent rooms |
| Zone transition | < 1s | Async load next zone tileset |
| Return to hub | < 2s | Hub always warm-loaded |

## Profiling Methodology

1. **Unity Profiler** — Frame-by-frame CPU/GPU/Memory
2. **Deep Profile** — Method-level allocation tracking (dev builds only)
3. **Custom ECS Profiler** — Per-system tick timing, entity counts
4. **Network Profiler** — Packet rates, sizes, rollback frequency
5. **FMOD Profiler** — Voice count, CPU usage, memory

## Red Lines (Never Cross)

- Frame time > 20ms for 3+ consecutive frames = **P0 bug**
- ECS tick > 5ms = **P0 bug**  
- Snapshot > 500μs = **P1 bug**
- Memory > 1.5GB on console = **P0 bug**
- Network > 50KB/s per player = **P1 bug**
- Load time > 10s anywhere = **P1 bug**
