# 18 — PERFORMANCE BUDGETS & PLATFORM TARGETS

## Frame Budget (16.67 ms at 60 FPS)

| Subsystem | Budget | Notes |
|---|---|---|
| ECS simulation tick | 2.0 ms | 60Hz fixed, must not exceed for determinism |
| Rollback re-simulation (per tick) | 2.0 ms × 10 ticks max | 20 ms worst case (causes frame skip, acceptable) |
| Snapshot serialize | 0.5 ms | 500 entities target |
| Snapshot deserialize | 0.5 ms | 500 entities target |
| Rendering (UE5) | 8.0 ms | 2D sprites + parallax, well within budget |
| Audio (FMOD + DSP) | 1.0 ms | Separate thread, async |
| Network I/O | 0.5 ms | Non-blocking UDP |
| Input processing | 0.2 ms | Trivial |
| UI update | 0.5 ms | HUD elements only during gameplay |
| **Total** | **~13.2 ms** | **3.5 ms headroom** |

## Memory Budget

| Component | Budget | Notes |
|---|---|---|
| ECS world state | 50 MB | 4096 entities × ~12 KB average |
| Snapshot ring buffer | 60 MB | 120 snapshots × ~500 KB each |
| Room tilemap (active) | 20 MB | Current room + 2 adjacent rooms loaded |
| Sprite atlas (loaded) | 100 MB | Current zone characters + enemies |
| Audio buffers | 30 MB | FMOD banks + VOIP jitter buffers |
| UI textures | 20 MB | HUD, menus, fonts |
| Parallax backgrounds | 80 MB | 4-5 layers, current zone |
| Network buffers | 5 MB | Input/snapshot/voice |
| **Total** | **~365 MB** | **Well within 4GB console target** |

## Platform Targets

| Platform | Resolution | FPS Target | Notes |
|---|---|---|---|
| PC (min spec) | 1080p | 60 FPS | GTX 1060 / RX 580 equivalent |
| PC (recommended) | 1440p/4K | 60 FPS | RTX 3060 / RX 6700 XT |
| PS5 | 4K | 60 FPS | Native |
| Xbox Series X | 4K | 60 FPS | Native |
| Xbox Series S | 1080p | 60 FPS | Reduced parallax layers |
| Cloud-Mobile | 1080p | 60 FPS | Streamed via Xbox Cloud / GFN |

## Loading Time Budgets
| Transition | Budget | Notes |
|---|---|---|
| Boot to main menu | < 5s | Async asset loading |
| Menu to Canopy hub | < 3s | Hub is always partially loaded |
| Canopy to delve start | < 2s | Room gen (100ms) + asset load |
| Room-to-room transition | < 500ms | Pre-load adjacent rooms |
| Death/Overload recovery | Instant | No load screen, in-place respawn |
