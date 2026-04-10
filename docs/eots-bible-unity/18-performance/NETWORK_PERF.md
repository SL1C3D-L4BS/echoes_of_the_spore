# Network Performance

## Bandwidth Targets

| Direction | Per Player | 4-Player Session Total |
|-----------|-----------|----------------------|
| Client upload | 5 KB/s | 20 KB/s |
| Client download | 15 KB/s | — (server sends to each) |
| Voice upload | 3 KB/s | 12 KB/s |
| Voice download (3 peers) | 9 KB/s | — |
| **Total per player** | **~23 KB/s** | — |
| **Server total (4 players)** | — | **~92 KB/s** |

## Packet Rate Targets

| Packet Type | Rate | Size (avg) | Bandwidth |
|-------------|------|-----------|-----------|
| InputMsg (client → server) | 60/s | 42 B | 2.5 KB/s |
| SyncMsg (server → client) | 15/s | 60 B | 0.9 KB/s |
| InputRelay (server → client) | 60/s | 168 B | 10.1 KB/s |
| VoiceMsg (peer/relay) | 50/s | 100 B | 5.0 KB/s |
| Ping/Pong | 1/s | 16 B | 0.016 KB/s |
| StateMsg (rare) | ~0.01/s | 4 KB | ~0.04 KB/s |

## Latency Targets

| Metric | Target | Alert | P0 |
|--------|--------|-------|-----|
| RTT (same region) | < 50ms | > 100ms | > 166ms |
| RTT (cross-region) | < 120ms | > 150ms | > 200ms |
| Input-to-screen (local) | < 50ms (3 frames) | > 83ms | > 100ms |
| Input-to-screen (remote) | < 100ms | > 150ms | > 200ms |
| Voice latency (end-to-end) | < 80ms | > 120ms | > 200ms |
| Matchmaking | < 15s (P95) | > 30s | > 60s |

## Rollback Performance

| Metric | Budget | Measurement |
|--------|--------|-------------|
| Rollback frequency | < 3/s per player | Counter |
| Rollback depth (avg) | < 3 ticks | Histogram |
| Rollback depth (max) | 10 ticks | Hard cap |
| Resimulation time (per tick) | 2.5ms | ECS tick time |
| Worst-case resim (10 ticks) | 25ms | Causes 1 frame skip |
| Frame skips per minute | < 2 | Counter |

## Packet Loss Handling

| Loss Rate | Impact | Mitigation |
|-----------|--------|-----------|
| 0-1% | Unnoticeable | Redundant input sends (3x) |
| 1-3% | Rare misprediction | Redundant sends cover most |
| 3-5% | Occasional corrections | Increase input delay to 3 |
| 5-10% | Frequent corrections | Show connection warning |
| 10%+ | Degraded experience | Show "poor connection" |

## Network Thread Performance

| Operation | Budget | Thread |
|-----------|--------|--------|
| Socket recv (non-blocking poll) | 0.1ms | Network thread |
| Packet deserialization | 0.05ms per packet | Network thread |
| Input buffer update | 0.02ms | Network thread |
| Socket send (all outgoing) | 0.1ms | Network thread |
| Voice packet processing | 0.05ms per player | Audio thread |
| **Total network CPU** | **< 0.5ms** | — |

## Compression Efficiency

| Data | Uncompressed | Compressed (zstd-3) | Ratio |
|------|-------------|---------------------|-------|
| StateMsg (100 entities) | 4.0 KB | 1.3 KB | 3.1:1 |
| StateMsg (500 entities) | 18.0 KB | 5.5 KB | 3.3:1 |
| StateMsg (1000 entities) | 35.0 KB | 10.2 KB | 3.4:1 |
| SpectateState | 3.5 KB | 1.1 KB | 3.2:1 |

Compression cost: ~0.1ms for 20 KB payload. Only applied to StateMsg.

## Connection Quality Monitoring

Real-time metrics tracked per player:

```csharp
public struct ConnectionQuality
{
    public float SmoothedRTT;        // EWMA, alpha=0.1
    public float Jitter;             // Std dev of RTT (last 60 samples)
    public float PacketLossPercent;  // Rolling 10s window
    public int RollbacksLast60s;     // Rolling counter
    public int DesyncCount;          // Session total
    public int FrameAdvantage;       // Local vs remote tick delta
    public ConnectionGrade Grade;    // Excellent/Good/Fair/Poor/Critical
}

public enum ConnectionGrade
{
    Excellent,  // RTT < 30ms, loss < 0.5%
    Good,       // RTT < 80ms, loss < 2%
    Fair,       // RTT < 120ms, loss < 5%
    Poor,       // RTT < 166ms, loss < 10%
    Critical    // RTT > 166ms or loss > 10%
}
```

## Server Network Performance

| Metric | Per Pod (8 sessions) | Alert |
|--------|---------------------|-------|
| Inbound bandwidth | ~160 KB/s | > 400 KB/s |
| Outbound bandwidth | ~800 KB/s | > 2 MB/s |
| Packets per second (in) | ~1,920/s | > 5,000/s |
| Packets per second (out) | ~7,680/s | > 20,000/s |
| Socket buffer usage | < 50% | > 80% |
| Packet processing latency | < 0.5ms | > 2ms |

## NAT Traversal Performance

| Method | Success Rate | Latency Added |
|--------|-------------|---------------|
| Direct (no NAT) | 100% | 0ms |
| STUN hole-punch | ~80% | 0-5ms |
| TURN relay | 100% | 10-30ms |
| Platform relay (Steam/PSN/Xbox) | 100% | 5-20ms |

Fallback chain: Direct → STUN → Platform Relay → TURN

## Optimization Techniques

| Technique | Savings | Applied To |
|-----------|---------|-----------|
| Redundant input (3x sends) | Covers 1 dropped packet | InputMsg |
| Delta compression | ~70% size reduction | StateMsg (future) |
| Bit packing | ~30% size reduction | InputCommand |
| Sequenced unreliable | No retransmit overhead | Most packets |
| DTX (voice) | ~90% bandwidth during silence | VoiceMsg |
| Adaptive jitter buffer | Minimize voice latency | Voice pipeline |

## Network Performance Red Lines

| Metric | Red Line | Action |
|--------|----------|--------|
| Per-player bandwidth > 50 KB/s | P1 | Investigate packet size |
| Server bandwidth > 5 MB/s per pod | P1 | Investigate packet rate |
| RTT variance > 50ms within 10s | P2 | Investigate jitter |
| Rollback > 10/s sustained | P1 | Increase input delay |
| State resync > 1/min | P0 | Investigate determinism |
| Matchmaking > 60s | P1 | Scale matchmaking service |
