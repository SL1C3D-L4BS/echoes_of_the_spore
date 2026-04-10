# Memory Optimization

## Memory Budget by Category

| Category | Budget | Strategy |
|----------|--------|----------|
| Unity Engine + IL2CPP | 150 MB | Unavoidable baseline |
| Custom ECS World | 50 MB | Fixed-size arrays, no managed alloc |
| Snapshot Ring Buffer | 60 MB | Pre-allocated, fixed capacity |
| Sprite Atlases | 80 MB | Load per-zone, unload on exit |
| Audio (FMOD) | 30 MB | Stream music, pre-load SFX |
| Tilemaps | 20 MB | 3 rooms max (current + 2 adjacent) |
| UI | 20 MB | Single canvas set, pooled elements |
| Parallax Backgrounds | 50 MB | 3-4 layers, compressed |
| Network Buffers | 5 MB | Fixed ring buffers |
| **Total** | **~465 MB** | **Target: < 500 MB** |

## Zero-Allocation Patterns

The ECS simulation must produce ZERO managed heap allocations during gameplay:

### Pre-Allocated Containers

| Container | Size | Usage |
|-----------|------|-------|
| Entity pool | 4096 slots | Max entity count |
| Component arrays | sizeof(T) x 4096 | One array per component type |
| Snapshot buffer | 120 x ~30 KB | Ring buffer for rollback |
| Input buffer | 64 x 12 B x 4 players | Per-player input history |
| Collision pairs | 1024 pairs | Spatial hash results |
| Event queue | 256 events | Inter-system communication |

### Patterns to Use

```csharp
// GOOD: Pre-allocated array, indexed access
private FixedPoint64[] _xPositions = new FixedPoint64[MAX_ENTITIES];

// GOOD: Struct component, no heap allocation
public struct Position : IComponent
{
    public FixedPoint64 X;
    public FixedPoint64 Y;
}

// GOOD: Span<T> for temporary slices (stack allocated)
Span<int> nearbyEntities = stackalloc int[64];
int count = _spatialHash.Query(pos, radius, nearbyEntities);

// GOOD: Object pool for GameObjects
private GameObjectPool _enemyPool = new(prefab, initialSize: 50);
```

### Patterns to Avoid

```csharp
// BAD: new allocates on heap
var list = new List<int>();  // HEAP ALLOCATION

// BAD: LINQ allocates enumerators
var result = entities.Where(e => e.IsAlive).ToList();  // HEAP ALLOCATION

// BAD: String concatenation in hot path
Debug.Log("Entity " + id + " at " + pos);  // HEAP ALLOCATION

// BAD: Boxing value types
object boxed = myStruct;  // HEAP ALLOCATION

// BAD: Lambda captures
entities.ForEach(e => Process(e, localVar));  // CLOSURE ALLOCATION
```

## GC Management Integration

| Metric | Budget | Enforcement |
|--------|--------|------------|
| Allocations per frame (gameplay) | 0 bytes in Core/ | Roslyn analyzer |
| Allocations per frame (Bridge) | < 256 bytes | Profile weekly |
| Allocations per frame (UI) | < 1 KB | Profile weekly |
| GC.Collect calls | Never during gameplay | Disabled via scripting |
| Incremental GC time slice | 1ms max | Unity setting |

## Sprite Memory Optimization

| Technique | Savings | Implementation |
|-----------|---------|---------------|
| Texture compression (ASTC/BC7) | 4:1 ratio | Import settings |
| Sprite atlas packing | Reduce texture count | Unity Sprite Atlas |
| Mip maps disabled | 33% per texture | 2D game, no distance LOD |
| Read/Write disabled | 50% per texture | No CPU-side access needed |
| Per-zone loading | Only 1 zone loaded | Addressables groups |
| Shared tilesets | Reuse tiles across variants | Tile palette design |

## Tilemap Memory

```
Per Room:
  6 layers × avg 1200 tiles × 4 bytes per tile index = ~28.8 KB per room
  + Collider data: ~5 KB per room
  + Renderer data: ~10 KB per room
  Total: ~44 KB per room

3 rooms loaded = ~132 KB tilemap data (negligible)
Tileset textures: ~5-8 MB per zone (dominant cost)
```

### Room Loading Strategy

```
Current Room: Fully loaded, all layers rendered
Adjacent Rooms (N/S/E/W): Loaded, collision active, rendering ready
All Other Rooms: Unloaded

On Room Transition:
  1. Identify new adjacent set
  2. Load new rooms (async, ~50ms each)
  3. Unload rooms no longer adjacent (immediate)
  4. Total memory delta: ~0 (swap)
```

## Audio Memory

| Strategy | Detail |
|----------|--------|
| Stream music | Never fully load music tracks into memory |
| Pre-load combat SFX | Always in memory for instant playback |
| Swap environment SFX | Load per-zone, unload on zone exit |
| Stream narrator VO | Load on demand, unload after playback |
| Spore dictionary | Always loaded (tiny: 3 MB) |
| FMOD sample pool | Fixed 128 MB cap |

## Network Buffer Memory

| Buffer | Size | Lifetime |
|--------|------|----------|
| Send buffer | 64 KB | Session |
| Receive buffer | 64 KB | Session |
| VOIP encode buffer | 960 samples x 4 bytes = 3.8 KB | Session |
| VOIP jitter buffer (per player) | 8 frames x 960 samples x 4 = 30 KB | Session |
| VOIP decode buffer (per player) | 960 samples x 4 = 3.8 KB | Session |
| Clip Mode buffers | 11.52 MB | Session |
| **Total network** | **~11.7 MB** | — |

## Memory Leak Detection

| Method | Frequency | Tool |
|--------|-----------|------|
| Memory Profiler snapshots | Before/after 10-min session | Unity Memory Profiler |
| Endurance test | Monthly, 4-hour session | Automated (see LOAD_TESTING.md) |
| Manual inspection | Weekly | Task Manager / top |
| GC pressure monitoring | Continuous (dev builds) | Custom profiler overlay |

### Leak Detection Procedure

```
1. Take memory snapshot at session start
2. Play for 10 minutes (varied gameplay)
3. Return to hub
4. Force GC.Collect() (debug build only)
5. Take memory snapshot
6. Compare: if delta > 5 MB, investigate
7. Diff snapshots to find growing allocations
```

## Platform Memory Limits

| Platform | Hard Limit | Our Budget | Headroom |
|----------|-----------|-----------|----------|
| PC (min spec 8GB) | ~4 GB usable | 500 MB | 3.5 GB |
| PS5 | 12 GB (shared GPU/CPU) | 500 MB game + 200 MB GPU | 11.3 GB |
| Xbox Series X | 10 GB (8 GB standard) | 500 MB | 7.5 GB |
| Xbox Series S | 8 GB (6 GB standard) | 400 MB | 5.6 GB |
| Switch 2 | 4 GB (estimated) | 350 MB | Conservative |
| Steam Deck | 16 GB (shared) | 500 MB | 15.5 GB |
