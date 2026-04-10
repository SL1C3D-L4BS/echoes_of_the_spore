# Desync Handling

## Detection Mechanisms

Desync occurs when client and server simulations diverge. Detection uses layered
checksum comparison at multiple granularities.

## Checksum System

| Level | Frequency | Scope | Hash Algorithm |
|-------|-----------|-------|---------------|
| World checksum | Every 4 ticks (66ms) | All entity state | xxHash32 |
| System checksum | On desync detected | Per-ECS-system state | xxHash32 |
| Entity checksum | On desync detected | Per-entity components | xxHash32 |
| Component checksum | Debug only | Individual component bytes | xxHash32 |

### World Checksum Computation

```csharp
public uint ComputeWorldChecksum(int tick)
{
    using var stream = new MemoryStream(4096);
    using var writer = new BinaryWriter(stream);
    
    writer.Write(tick);
    foreach (var entity in _world.AllEntities())
    {
        writer.Write(entity.Id);
        foreach (var component in entity.AllComponents())
            component.Serialize(writer);  // Deterministic order
    }
    
    return XxHash32.Hash(stream.GetBuffer(), 0, (int)stream.Length);
}
```

## Detection Flow

```
Client computes checksum for tick N
Server broadcasts SyncMsg with checksum for tick N
Client compares:
  Match → continue
  Mismatch → DESYNC DETECTED
    → Log tick N, local checksum, server checksum
    → Request StateMsg from server
    → Enter recovery mode
```

## Recovery Procedure

| Step | Action | Duration |
|------|--------|----------|
| 1 | Client sends DesyncReport to server | Immediate |
| 2 | Server sends full StateMsg for current tick | 1-2 frames |
| 3 | Client receives StateMsg | Network dependent |
| 4 | Client halts simulation | 1 frame |
| 5 | Client deserializes server state | < 0.5ms |
| 6 | Client replaces local world state | Instant |
| 7 | Client resumes simulation from server tick | Next frame |
| 8 | Visual correction applied (lerp remote, instant local) | 3-5 frames |

## DesyncReport Message

```
DesyncReport (sent client → server):
  [Header: 4B]
  [type: 1B]              (DESYNC_REPORT = 6)
  [tick: 4B]              (tick where mismatch detected)
  [client_checksum: 4B]   (client's computed hash)
  [server_checksum: 4B]   (hash from SyncMsg)
  [entity_count: 2B]      (client's entity count)
  [system_checksums: var]  (per-system hashes for diagnosis)
  Total: ~64-128 bytes
```

## Desync Diagnosis (Server-Side Logging)

When a DesyncReport is received, the server logs detailed diagnostics:

| Data Logged | Purpose |
|------------|---------|
| Tick number | When the desync occurred |
| All player inputs for tick range (N-10 to N) | Identify input delivery issues |
| Entity diff (server vs client entity count) | Missing/extra entity |
| Per-system checksums comparison | Narrow to specific system |
| Recent rollback history | Correlate with rollback events |
| Network stats (RTT, packet loss) | Environmental factors |

## Common Desync Causes

| Cause | Symptom | Fix |
|-------|---------|-----|
| Floating-point arithmetic | Gradual drift | Use FixedPoint64 exclusively |
| Non-deterministic iteration | Random divergence | Sorted entity iteration |
| Uninitialized state | Immediate desync | Zero-init all components |
| Platform-specific math | Cross-platform mismatch | Soft-float or FP64 tables |
| Race condition | Intermittent desync | Single-threaded simulation |
| RNG sequence mismatch | Deterministic but wrong | Verify xoshiro256** seed sync |
| Missed input | Prediction → wrong state | Redundant input send (3x) |

## Desync Rate Targets

| Phase | Max Desync Rate | Action if Exceeded |
|-------|----------------|-------------------|
| Development | < 5% of sessions | Log and investigate |
| Alpha | < 2% of sessions | P1 bug ticket |
| Beta | < 0.5% of sessions | P0 hotfix |
| Launch | < 0.1% of sessions | Immediate investigation |
| Live | < 0.05% of sessions | Monitoring alert |

## Telemetry

Every desync event is reported to the analytics backend:

```json
{
    "event": "desync",
    "session_id": "uuid",
    "tick": 14523,
    "client_hash": "0xA3B2C1D0",
    "server_hash": "0xA3B2C1D1",
    "rtt_ms": 87,
    "packet_loss_pct": 2.1,
    "player_count": 4,
    "zone_id": 3,
    "rollback_count_last_60s": 12,
    "platform": "windows",
    "build_version": "0.4.2"
}
```

## Player Experience During Desync

| Aspect | Behavior |
|--------|----------|
| Visual | Brief stutter (1-2 frames), then smooth correction |
| Audio | No interruption (audio is non-deterministic) |
| Input | Continues accepting input during recovery |
| HUD | No desync indicator shown to player |
| Debug build | Yellow "DESYNC" overlay with tick info (F3 debug) |

## Prevention Strategies

| Strategy | Implementation |
|----------|---------------|
| Deterministic math | FixedPoint64 for all simulation math |
| Deterministic RNG | xoshiro256** with synced seeds |
| Deterministic iteration | Entity IDs sorted, system execution order fixed |
| Input validation | Server validates and broadcasts canonical inputs |
| Snapshot verification | Periodic checksum comparison |
| CI determinism tests | Two identical sims, 1000 ticks, assert equal |
| Cross-platform CI | Run determinism tests on Windows + Linux + macOS |
