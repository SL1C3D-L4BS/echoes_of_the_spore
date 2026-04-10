# Session Architecture

## Overview

Every multiplayer delve is a single instanced session. Sessions are not persistent world shards — they exist only for the duration of one delve attempt and are destroyed on completion or failure.

## Session Lifecycle

```
LOBBY → LOADING → ACTIVE → BOSS → RESULT → DISBAND
```

| State | Duration | Description |
|---|---|---|
| LOBBY | 0-120s | Host creates session. Matchmaking fills slots. Players confirm loadout. |
| LOADING | 5-15s | Zone chunk streaming. Seed synchronization. Entity spawn list broadcast. |
| ACTIVE | Variable | Exploration and combat. Rooms load/unload per proximity. |
| BOSS | Variable | Boss room sealed. No join-in-progress allowed. |
| RESULT | 10s | Loot distribution, XP tally, Infection delta displayed. |
| DISBAND | 2s | Redis key TTL set to 60s for reconnect grace. Session destroyed. |

## Instance Management

- Each session is assigned a unique `session_id` (UUID v4)
- Redis hash key: `session:{session_id}` stores authoritative state
- TTL on Redis key: 3600s (auto-cleanup of abandoned sessions)
- Max concurrent sessions per region: 10,000 (horizontal scaling via Redis Cluster)

## Player Capacity

| Parameter | Value |
|---|---|
| Min players | 1 (solo) |
| Max players | 4 |
| Join-in-progress | Allowed during ACTIVE state only |
| Backfill window | First 50% of zone rooms cleared |
| Reconnect grace | 60 seconds after disconnect |

## Redis State Schema

```
session:{id}/state        → enum (LOBBY|LOADING|ACTIVE|BOSS|RESULT|DISBAND)
session:{id}/host         → player_id
session:{id}/players      → sorted set (player_id, join_timestamp)
session:{id}/zone         → int (1-8)
session:{id}/seed         → int64
session:{id}/room_states  → hash (room_id → json blob)
session:{id}/tick         → int64 (server tick counter)
session:{id}/created_at   → unix timestamp
```

## Tick Model

- Server tick rate: 60 Hz
- Client send rate: 30 Hz (every other tick)
- Snapshot interpolation buffer: 100ms (6 ticks)
- Input prediction window: 4 ticks (66ms)

## Host Migration

If the host disconnects:
1. Server selects lowest-latency remaining player as new host
2. Full state snapshot sent from Redis (not peer-to-peer)
3. Migration completes in < 500ms
4. If all players disconnect, session enters 60s grace period then DISBAND

## Session Events (Redis Pub/Sub)

| Event | Payload | Consumers |
|---|---|---|
| `player_join` | player_id, loadout_hash | All clients, matchmaker |
| `player_leave` | player_id, reason | All clients, matchmaker |
| `state_change` | old_state, new_state | All clients, metrics |
| `room_enter` | room_id, player_id | Ecosystem sim, AI director |
| `boss_start` | boss_id | Matchmaker (blocks backfill) |
| `session_end` | result, duration, loot | Metrics, leaderboard |

## Failure Handling

| Scenario | Behavior |
|---|---|
| Server crash | Redis state persists. New server picks up session within 5s. |
| All players disconnect | 60s grace. If no reconnect, DISBAND. Loot lost. |
| Player timeout (no input 120s) | Kicked. Slot opened for backfill. |
| Desync detected | Server sends full state correction. Client rubber-bands. |

## Network Topology

```
[Client A] ──┐
[Client B] ──┤──→ [Dedicated Server] ──→ [Redis Cluster]
[Client C] ──┤                              ↕
[Client D] ──┘                        [Matchmaker Service]
```

All game logic runs server-side. Clients are thin: input submission + rendering + prediction.

## Implementation Notes

- Unity: Use Netcode for GameObjects with custom transport layer over UDP
- Serialize snapshots with MessagePack (smaller than Protobuf for our entity shapes)
- Room transitions use additive scene loading with `LoadSceneMode.Additive`
- Redis client: StackExchange.Redis for .NET / dedicated server, NRedisStack for monitoring tools
