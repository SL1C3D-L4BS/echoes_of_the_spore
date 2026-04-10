# Snapshot Serialization

## Purpose
Snapshot serialization captures the complete ECS world state for:
1. **Rollback:** Restore to previous tick and resimulate
2. **Checksum:** Verify client/server determinism (xxHash32)
3. **Spectator:** Send full state to late-joining spectators
4. **Replay:** Record/playback entire matches

## Performance Target
**< 500 microseconds** for 500 entities with average 3 components each.

## Format

```
WorldSnapshot
├── Tick (int32)                           4 bytes
├── EntityCount (int16)                    2 bytes
├── ComponentTypeCount (int8)              1 byte
├── For each component type:
│   ├── TypeId (int8)                      1 byte
│   ├── EntryCount (int16)                 2 bytes
│   └── For each entry:
│       ├── EntityId (int16)               2 bytes
│       └── ComponentData (sizeof(T))      varies
└── Checksum (uint32, xxHash32)            4 bytes
```

## Size Estimates

| Component | Size (bytes) | Count (500 entities) | Total |
|-----------|-------------|---------------------|-------|
| TransformComponent | 16 (2 × FP64) | 500 | 8,000 |
| VelocityComponent | 16 | 300 | 4,800 |
| HealthComponent | 8 | 200 | 1,600 |
| CollisionComponent | 20 | 400 | 8,000 |
| InputComponent | 24 | 4 | 96 |
| InfectionComponent | 8 | 4 | 32 |
| StatusEffectComponent | 20 | 100 | 2,000 |
| AIComponent | 12 | 200 | 2,400 |
| **Overhead** | ~2,000 | — | 2,000 |
| **Total** | — | — | **~29 KB** |

## Ring Buffer

```csharp
public class SnapshotRingBuffer
{
    private WorldSnapshot[] _buffer;
    private int _head;
    private const int CAPACITY = 120;  // 2 seconds at 60Hz

    public void Push(WorldSnapshot snapshot);
    public WorldSnapshot Get(int ticksAgo);  // 0 = current, 10 = max rollback
    public bool HasTick(int tick);
}
```

## Serialization Strategy

```csharp
// Fast binary serialization using Span<byte>
public static int Serialize(EotSWorld world, Span<byte> buffer)
{
    var writer = new SpanWriter(buffer);
    writer.WriteInt32(world.CurrentTick);
    writer.WriteInt16((short)world.EntityCount);

    // Serialize each component storage contiguously
    SerializeStorage<TransformComponent>(world, ref writer);
    SerializeStorage<VelocityComponent>(world, ref writer);
    // ... all component types

    // Compute checksum over entire serialized data
    uint checksum = XxHash32.Compute(buffer[..writer.Position]);
    writer.WriteUInt32(checksum);

    return writer.Position;
}
```

## Checksum Validation

Every 4 ticks (66ms), clients send their checksum to the server.

| Outcome | Action |
|---------|--------|
| Checksums match | Continue normally |
| Mismatch within rollback window (10 ticks) | Client rollbacks to last known-good state |
| Mismatch beyond rollback window | Full state resync from server (~29KB) |
| 3 consecutive mismatches | Flag as potential desync, log diagnostics |
