# Console Optimization

## Platform Targets

| Platform | Resolution | FPS | CPU Budget | GPU Budget | Memory |
|----------|-----------|-----|-----------|-----------|--------|
| PS5 | 4K | 60 | 8ms | 8ms | 5 GB max |
| Xbox Series X | 4K | 60 | 8ms | 8ms | 5 GB max |
| Xbox Series S | 1080p | 60 | 10ms | 10ms | 3.5 GB max |
| Switch 2 | 1080p/720p | 60 | 12ms | 12ms | 3 GB max |

## PS5 Optimizations

| Optimization | Detail | Impact |
|-------------|--------|--------|
| SSD streaming | Use PS5 I/O API for asset streaming | Load times < 1s |
| DualSense haptics | Haptic feedback for attacks, infection | Player experience |
| Activity cards | Track boss progress, zone completion | PS5 UX integration |
| Tempest 3D audio | Not used (2D game, stereo output) | N/A |
| IL2CPP optimizations | Zen 2 specific code gen | 5-10% CPU improvement |
| Shader compilation | Pre-compile all shader variants | No runtime stutter |
| Trophy integration | All trophies mapped | Cert requirement |

### PS5 Memory Layout

| Category | Allocation | Notes |
|----------|-----------|-------|
| Game (CPU) | 500 MB | Standard game budget |
| GPU (VRAM shared) | 200 MB | Sprites, tilemaps, buffers |
| Audio | 128 MB | FMOD banks |
| System reserved | 512 MB | OS, party, notifications |
| I/O staging buffer | 32 MB | SSD streaming |
| **Total used** | **~1.4 GB** | **Well under 12 GB limit** |

## Xbox Series Optimizations

| Optimization | Series X | Series S |
|-------------|----------|----------|
| Resolution | 4K native | 1080p native |
| Parallax layers | 4 layers | 3 layers (reduce 1) |
| Sprite atlas size | 4096x4096 | 2048x2048 |
| Audio banks | Full load | Stream music |
| Particle budget | 150 max | 100 max |
| 2D lights | 8 max | 6 max |
| Quick Resume | Supported | Supported |
| Smart Delivery | Auto-select build | Auto-select build |

### Xbox Quick Resume

| Requirement | Implementation |
|------------|---------------|
| State preservation | Full ECS world state serialized on suspend |
| Network reconnect | Automatic reconnect on resume (10s grace) |
| Audio restart | FMOD system re-initialized on resume |
| Memory cleanup | Incremental GC on resume |
| Resume time | < 5s to playable state |

## Switch 2 Optimizations

| Optimization | Docked | Handheld |
|-------------|--------|---------|
| Resolution | 1080p | 720p |
| FPS target | 60 | 60 |
| Parallax layers | 3 | 2 |
| Sprite atlas size | 2048x2048 | 2048x2048 |
| Max entities | 3072 (reduced) | 2048 (reduced) |
| 2D lights | 4 max | 3 max |
| Particle budget | 80 max | 60 max |
| Audio | Stream music + narrator | Stream all audio |
| Post-processing | Bloom only | None |
| VOIP | Headset mic only | Headset mic only |

### Switch 2 Memory Budget

| Category | Budget | Notes |
|----------|--------|-------|
| Game | 350 MB | Reduced entity pool, fewer atlases |
| GPU | 150 MB | Smaller textures |
| Audio | 80 MB | Stream everything possible |
| System | 256 MB | OS, home menu overlay |
| **Total** | **~836 MB** | **Under estimated 4 GB** |

### Switch 2 CPU Optimization

```
Simulation: 60Hz fixed tick (identical to other platforms)
Rendering: 60fps target
  If frame time > 16.67ms for 3 frames:
    → Enable dynamic resolution (scale to 900p docked, 640p handheld)
    → Reduce particle count by 50%
    → Disable non-essential post-processing
```

## Cross-Platform Quality Settings

| Setting | PC High | PC Low | PS5/Xbox X | Xbox S | Switch 2 |
|---------|---------|--------|------------|--------|----------|
| Resolution | 4K | 1080p | 4K | 1080p | 1080p/720p |
| Parallax layers | 4 | 3 | 4 | 3 | 2-3 |
| 2D light count | 8 | 6 | 8 | 6 | 3-4 |
| Particle max | 200 | 100 | 150 | 100 | 60-80 |
| Post-processing | Full | Bloom only | Full | Bloom+vignette | Bloom only |
| Audio quality | Full banks | Full banks | Full banks | Stream music | Stream all |
| Shadow quality | Soft 2D | Hard 2D | Soft 2D | Hard 2D | None |
| Sprite atlas size | 4096 | 2048 | 4096 | 2048 | 2048 |
| Max entities | 4096 | 4096 | 4096 | 4096 | 2048-3072 |

## Build Configuration Per Platform

| Setting | All Platforms |
|---------|--------------|
| Scripting backend | IL2CPP |
| API compatibility | .NET Standard 2.1 |
| Managed stripping | Medium |
| Incremental GC | Enabled |
| Script debugging | Disabled (Release) |
| Profiler | Disabled (Release) |
| Stack trace | ScriptOnly (Release) |

## Platform-Specific Build Flags

```csharp
#if UNITY_PS5
    // PS5-specific initialization
    SonySDK.Initialize();
    ActivityCard.Register();
#elif UNITY_GAMECORE_XBOXONE || UNITY_GAMECORE_SCARLETT
    // Xbox-specific initialization
    GDK.Initialize();
    SmartDelivery.Configure();
#elif UNITY_SWITCH
    // Switch-specific initialization
    NintendoSDK.Initialize();
    nn.account.Initialize();
#endif
```

## Console Profiling Schedule

| Phase | Frequency | Focus |
|-------|-----------|-------|
| Pre-production | Monthly | Baseline measurement on target hardware |
| Vertical Slice | Bi-weekly | Identify early bottlenecks |
| Alpha | Weekly | Hit budget targets |
| Beta | Daily | Console-specific issues |
| Polish | Daily | Final optimization pass |
| Pre-cert | Continuous | Meet all cert requirements |
