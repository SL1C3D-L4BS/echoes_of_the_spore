# Rollback Netcode Architecture

## Model: Client-Prediction + Server-Authoritative + Rollback

```
CLIENT A                    SERVER                    CLIENT B
─────────                   ──────                    ─────────
Predict locally ──Input──→  Validate           ←──Input── Predict locally
                           Broadcast all inputs
         ←──AllInputs──    (tick N)              ──AllInputs──→
Compare prediction          Authoritative          Compare prediction
  Match? Continue             state                  Match? Continue
  Mismatch? Rollback                                 Mismatch? Rollback
    → Restore snapshot                                 → Restore snapshot
    → Resimulate ticks                                 → Resimulate ticks
    → Correct visuals                                  → Correct visuals
```

## Configuration

| Parameter | Value | Notes |
|-----------|-------|-------|
| Tick rate | 60 Hz | Fixed, non-negotiable |
| Input delay | 2 ticks (33ms) | Configurable 1-3 |
| Rollback window | 10 ticks (166ms) | Max resimulation depth |
| Snapshot buffer | 120 frames (2s) | Ring buffer capacity |
| Checksum frequency | Every 4 ticks (66ms) | xxHash32 |
| Stale packet threshold | 30 ticks (500ms) | Drop packets older than this |
| Input send rate | Every tick (60/s) | Redundant: sends last 3 ticks |
| State resync threshold | 10+ tick mismatch | Full snapshot transfer |

## Packet Types

```
InputMsg:   type(1B) + player_id(1B) + tick(4B) + input_count(1B) + [InputCommand × N]
StateMsg:   type(1B) + tick(4B) + compressed_snapshot(var)
SyncMsg:    type(1B) + tick(4B) + checksum(4B)
VoiceMsg:   type(1B) + player_id(1B) + seq(2B) + length(2B) + opus_data(var)
```

## Rollback Flow (Per Tick)

```
1. Read network: receive all pending InputMsg/StateMsg
2. For each remote player:
   a. If received input for tick N:
      - Compare with predicted input for tick N
      - If different: set rollback_needed = true, rollback_tick = N
3. If rollback_needed:
   a. Restore snapshot at rollback_tick
   b. Replace predicted inputs with actual inputs
   c. Resimulate from rollback_tick to current_tick (max 10 ticks)
   d. Update visuals (smooth correction, not teleport)
4. Apply local input for current_tick (predicted)
5. Tick simulation
6. Save snapshot to ring buffer
7. Send local input to server
8. Every 4 ticks: compute and send checksum
```

## Visual Correction

When rollback corrects entity positions:
- **Player character:** Instant correction (player expects responsiveness)
- **Remote players:** Lerp correction over 3 frames (avoids teleport jitter)
- **Projectiles:** Instant correction (fast-moving, lerp looks wrong)
- **Enemies:** Lerp correction over 5 frames (least jarring)

## Latency Handling

| Network RTT | Experience | Notes |
|------------|-----------|-------|
| 0-30ms | Perfect | Local/LAN, no visible rollbacks |
| 30-80ms | Good | Occasional minor corrections, unnoticeable |
| 80-120ms | Acceptable | Visible corrections on fast actions, playable |
| 120-166ms | Degraded | Frequent rollbacks, within window, playable |
| 166ms+ | Full resync | Beyond rollback window, lag spikes visible |

## Server Architecture

```
Dedicated Server (Unity Headless or .NET Console)
├── SessionManager: manages active game sessions
├── Per-Session:
│   ├── EotSWorld (authoritative simulation)
│   ├── InputBuffer[4] (one per player, stores last 30 ticks)
│   ├── UDP Socket (one per session)
│   └── ChecksumLog (last 120 checksums for desync diagnosis)
└── Redis Connection: matchmaking, session state, Community Tree
```
