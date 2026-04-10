# 18 — PERFORMANCE BUDGETS & PLATFORM TARGETS

## Frame Budget (16.67 ms at 60 FPS)

| Subsystem | Budget | Notes |
|---|---|---|
| Custom ECS tick (C#) | 2.5 ms | 60Hz fixed, FixedPoint64, 4096 max entities |
| Rollback re-sim (worst) | 25.0 ms (2.5 × 10) | Causes frame skip, acceptable |
| Snapshot serialize | 0.5 ms | 500 entities |
| Snapshot deserialize | 0.5 ms | 500 entities |
| Unity rendering (URP 2D) | 4.0 ms | SpriteRenderer + Tilemap + 2D Lights |
| Unity scene/GO overhead | 1.5 ms | Transform updates, MonoBehaviour callbacks |
| FMOD audio | 1.0 ms | Separate thread |
| Network I/O | 0.5 ms | Non-blocking |
| Input processing | 0.2 ms | Unity Input System |
| UI (Canvas/UITK) | 0.5 ms | HUD elements |
| **Total** | **~11.2 ms** | **5.5 ms headroom** |

## Memory Budget
| Component | Budget | Notes |
|---|---|---|
| Unity engine + Mono/IL2CPP | 150 MB | Managed heap + native |
| Custom ECS state | 50 MB | 4096 entities |
| Snapshot ring buffer | 60 MB | 120 × ~500 KB |
| Tilemap (active rooms) | 20 MB | Current + 2 adjacent |
| Sprite atlases | 80 MB | Current zone |
| Audio | 30 MB | FMOD banks + VOIP |
| UI | 20 MB | Canvas, fonts, textures |
| Parallax | 50 MB | Background layers |
| Network | 5 MB | Buffers |
| **Total** | **~465 MB** | **Within 4GB console target** |

## Platform Targets
| Platform | Resolution | FPS | Build |
|---|---|---|---|
| PC (min) | 1080p | 60 | IL2CPP |
| PC (rec) | 1440p/4K | 60 | IL2CPP |
| PS5 | 4K | 60 | IL2CPP + Sony SDK |
| Xbox Series X | 4K | 60 | IL2CPP + GDK |
| Xbox Series S | 1080p | 60 | Reduced parallax |
| Switch 2 | 1080p/720p | 60 | IL2CPP + Nintendo SDK |
| Steam Deck | 1280×800 | 60 | Linux IL2CPP |
| Cloud-Mobile | 1080p | 60 | Streamed |

## Loading Times
| Transition | Budget | Notes |
|---|---|---|
| Boot to menu | < 5s | Unity splash + async load |
| Menu to Canopy | < 3s | Scene load + Addressables |
| Canopy to delve | < 2s | Room gen + prefab instantiate |
| Room-to-room | < 500ms | Pre-instantiate adjacent |
| Death/Overload | Instant | No scene change |

## Build Configuration
- Scripting Backend: **IL2CPP** (all platforms) — required for console, better perf than Mono
- API Compatibility: .NET Standard 2.1
- Managed Stripping: Medium (preserve reflection for serialization)
- Incremental GC: Enabled
- Script debugging: Disabled in Release builds
