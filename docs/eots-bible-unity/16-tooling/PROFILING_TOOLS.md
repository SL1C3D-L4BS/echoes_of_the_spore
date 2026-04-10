# Profiling Tools

## Profiling Stack

| Tool | Purpose | When Used |
|------|---------|-----------|
| Unity Profiler | Frame CPU/GPU/Memory breakdown | Daily development |
| Unity Memory Profiler | Heap snapshots, leak detection | Weekly review |
| Deep Profile | Method-level allocation tracking | On demand (dev builds) |
| Custom ECS Profiler | Per-system tick timing | Always active (dev) |
| Network Profiler | Packet rates, rollback frequency | Multiplayer testing |
| FMOD Profiler | Audio voice count, CPU, memory | Audio tuning sessions |
| RenderDoc | GPU frame capture, shader debug | Rendering issues |
| Superluminal / Tracy | Native code profiling (IL2CPP) | Console optimization |

## Custom ECS Profiler

Built-in profiler overlay for the custom ECS simulation.

### Tracked Metrics

| Metric | Collection Method | Display |
|--------|------------------|---------|
| Per-system tick time (us) | Stopwatch per system | Bar chart |
| Total ECS tick time (us) | Sum of all systems | Single value |
| Entity count (by archetype) | World query | Table |
| Component memory usage | sizeof × count | Table |
| Rollback count (last 60s) | Counter | Rolling graph |
| Rollback depth (ticks) | Per-rollback | Histogram |
| Snapshot serialize time | Stopwatch | Single value |
| Snapshot size (bytes) | Measured | Single value |

### System Timing Table (Target)

| System | Budget (us) | Alert (us) | P0 (us) |
|--------|------------|-----------|---------|
| MovementSystem | 200 | 400 | 600 |
| CollisionSystem | 300 | 600 | 1000 |
| CombatSystem | 250 | 500 | 800 |
| DamageSystem | 100 | 200 | 400 |
| StatusEffectSystem | 150 | 300 | 500 |
| InfectionSystem | 100 | 200 | 400 |
| FusionSystem | 100 | 200 | 400 |
| AISystem | 300 | 600 | 1000 |
| ProjectileSystem | 150 | 300 | 500 |
| SpawnSystem | 50 | 100 | 200 |
| **Total** | **1700** | **3400** | **5000** |

### Overlay Toggle

| Key | Action |
|-----|--------|
| F1 | Toggle ECS profiler overlay |
| F2 | Toggle network profiler overlay |
| F3 | Toggle input buffer debug |
| F4 | Toggle entity inspector |
| F5 | Capture frame to log file |
| F6 | Toggle memory profiler |

## Network Profiler Overlay

| Display | Content |
|---------|---------|
| RTT graph | 60s rolling RTT with min/max/avg |
| Packet rates | In/out packets per second |
| Bandwidth | In/out KB/s |
| Rollback graph | Rollback events on timeline |
| Prediction accuracy | Rolling percentage |
| Desync counter | Total desyncs this session |

## Memory Tracking

```csharp
public static class MemoryTracker
{
    public static long ECSWorldBytes => _world.MemoryUsage;
    public static long SnapshotBufferBytes => _snapshots.MemoryUsage;
    public static long NetworkBufferBytes => _netBuffers.MemoryUsage;
    
    public static void LogSnapshot()
    {
        Debug.Log($"[MEM] ECS={ECSWorldBytes/1024}KB " +
                  $"Snap={SnapshotBufferBytes/1024}KB " +
                  $"Net={NetworkBufferBytes/1024}KB");
    }
}
```

## Performance Recording

Continuous metrics saved to JSON for post-session analysis:

```json
{
    "session_id": "uuid",
    "frames": [
        {
            "tick": 1234,
            "ecs_time_us": 1850,
            "render_time_us": 3200,
            "entity_count": 342,
            "rollback_depth": 0,
            "memory_mb": 412,
            "rtt_ms": 45
        }
    ]
}
```

| Recording Setting | Value |
|------------------|-------|
| Sample rate | Every 60 ticks (1/s) |
| Max file size | 10 MB |
| Auto-rotate | Keep last 10 sessions |
| Location | `{persistentDataPath}/Profiling/` |

## Platform-Specific Profiling

| Platform | Primary Tool | Secondary | Connection |
|----------|-------------|-----------|------------|
| PC (Editor) | Unity Profiler (local) | RenderDoc | Direct |
| PC (Build) | Unity Profiler (remote) | Superluminal | TCP localhost |
| PS5 | Razor (Sony) | Unity Profiler remote | Dev kit network |
| Xbox | PIX | Unity Profiler remote | Dev kit network |
| Switch 2 | NVN profiler | Unity Profiler remote | Dev kit USB |
| Steam Deck | Unity Profiler remote | perf | SSH |

## Automated Performance Tests

Run nightly in CI:

| Test | Metric | Pass Criteria |
|------|--------|--------------|
| 1000-tick benchmark | Avg ECS tick | < 2.5ms |
| 500-entity stress | Max ECS tick | < 5.0ms |
| Snapshot serialize 500 entities | Time | < 500us |
| Room generation (100 rooms) | Time | < 200ms total |
| Memory after 10min sim | Total | < 500 MB |
