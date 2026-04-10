# Connection Lifecycle

## Overview

A player's connection goes through distinct states from matchmaking to session end.
Each state has defined timeouts, retry policies, and failure handling.

## State Machine

```
[Disconnected]
    │ Player clicks "Play"
    ▼
[Matchmaking]
    │ Match found (< 30s)
    ▼
[Connecting]
    │ UDP handshake (< 5s)
    ▼
[Synchronizing]
    │ Full state received (< 3s)
    ▼
[Playing]
    │ Game in progress
    ├──→ [Reconnecting] (on packet loss > 5s)
    │        │ Resume (< 10s) → [Playing]
    │        │ Timeout → [Disconnected]
    ▼
[SessionEnd]
    │ Results saved
    ▼
[Disconnected]
```

## State Details

### Disconnected → Matchmaking

| Step | Action | Timeout |
|------|--------|---------|
| 1 | Client sends MatchRequest to matchmaking service (HTTPS) | — |
| 2 | Matchmaker queues player by region + MMR | — |
| 3 | Wait for 4-player match (or party fill) | 30s initial |
| 4 | If timeout, expand MMR range (+50 per 10s) | 60s max |
| 5 | If 60s timeout, offer solo + bots option | — |
| 6 | Match found: receive server address + session token | — |

### Connecting (UDP Handshake)

```
Client                           Server
  │                                │
  │──── ConnectRequest ──────────→ │  (token + client version)
  │                                │  Validate token
  │←─── ConnectAccept ────────────│  (player_slot, tick_rate, session_id)
  │                                │
  │──── ConnectAck ──────────────→ │  (confirms receipt)
  │                                │
  │     [CONNECTED]                │  [PLAYER JOINED]
```

| Parameter | Value |
|-----------|-------|
| Handshake timeout | 5s |
| Retry interval | 500ms |
| Max retries | 10 |
| On failure | Return to matchmaking with error |

### Synchronizing

| Step | Action | Duration |
|------|--------|----------|
| 1 | Server sends full StateMsg (current world state) | < 500ms |
| 2 | Client deserializes and initializes world | < 200ms |
| 3 | Client begins receiving SyncMsg + InputMsg | Immediate |
| 4 | Client starts simulation at server's current tick | Immediate |
| 5 | Client reports ready to server | < 100ms |
| 6 | Server broadcasts "player N joined" to all | Immediate |

Wait gate: All 4 players must reach [Playing] before simulation begins.
If a player fails to sync within 10s, they are dropped and replaced with bot.

### Playing (Steady State)

| Ongoing Action | Frequency |
|---------------|-----------|
| Send InputMsg | Every tick (60/s) |
| Receive SyncMsg | Every 4 ticks (15/s) |
| Receive InputRelay | Every tick (60/s) |
| Send/receive VoiceMsg | 50/s (when speaking) |
| Ping/Pong RTT measurement | Every 1s |
| Infection level sync (Redis) | Every 500ms |

### Playing → Reconnecting

Triggered when no packets received for 3 consecutive seconds.

| Step | Action | Timeout |
|------|--------|---------|
| 1 | Show "Reconnecting..." overlay to player | — |
| 2 | Client continues local simulation (predicted) | — |
| 3 | Client sends reconnect packets every 500ms | 10s max |
| 4 | Server holds player slot for 15s | — |
| 5a | If reconnected: server sends StateMsg, client resyncs | < 2s |
| 5b | If timeout: server replaces with bot, client → Disconnected | — |

### Session End

| Step | Action |
|------|--------|
| 1 | Server detects end condition (boss cleared, all dead, timer) |
| 2 | Server broadcasts SessionEnd message with results |
| 3 | Client shows results screen (stats, loot, echoes earned) |
| 4 | Client uploads telemetry (async, non-blocking) |
| 5 | Server submits Echo contribution to Community Tree (Supabase) |
| 6 | Client disconnects after 30s or player input |
| 7 | Server cleans up session resources |

## Connection Token

```json
{
    "session_id": "uuid-v4",
    "player_id": "uuid-v4",
    "slot": 2,
    "server_addr": "10.0.1.45:7777",
    "issued_at": 1712700000,
    "expires_at": 1712700060,
    "version": "0.4.2",
    "signature": "hmac-sha256"
}
```

| Field | Validation |
|-------|-----------|
| session_id | Must match active session on server |
| player_id | Must match authenticated user |
| expires_at | Must be in the future |
| version | Must match server version exactly |
| signature | HMAC-SHA256 with shared secret |

## Timeout Configuration

| Timeout | Duration | Action |
|---------|----------|--------|
| Matchmaking initial | 30s | Expand search criteria |
| Matchmaking max | 60s | Offer solo + bots |
| UDP handshake | 5s | Retry / fail |
| State sync | 10s | Drop player, replace with bot |
| Heartbeat (playing) | 3s no packets | Enter reconnecting |
| Reconnect window | 15s | Drop player |
| Session end screen | 30s | Auto-disconnect |
| Idle timeout (in hub) | 5 min | Kick with warning |

## Bot Replacement

When a player disconnects during a session:

| Aspect | Behavior |
|--------|----------|
| Transition | 1-frame switch, bot takes over entity |
| Bot AI | Basic combat AI, follows nearest player |
| Infection | Frozen at last known level |
| Fusion | Cannot initiate fusion, can be fused with |
| Slot reservation | 15s for reconnect |
| Notification | "Player X disconnected. Bot active." |
| Loot/Echoes | Disconnected player receives 50% rewards if session completes |

## Platform-Specific Connection Notes

| Platform | NAT Traversal | Relay Fallback | Notes |
|----------|--------------|---------------|-------|
| PC | STUN + hole punch | Steam Relay (Valve network) | Most reliable |
| PS5 | PSN NAT negotiation | PSN relay | Sony SDK handles |
| Xbox | Xbox STUN | Xbox relay | GDK networking |
| Switch 2 | Nintendo NAT | Nintendo relay | NEX framework |
| Cross-play | STUN first | Custom relay server | Fallback to relay |

## Error Codes

| Code | Meaning | Player-Facing Message |
|------|---------|----------------------|
| E001 | Matchmaking timeout | "Could not find a match. Try again?" |
| E002 | Version mismatch | "Game update required." |
| E003 | Server full | "Server is full. Retrying..." |
| E004 | Auth failure | "Session expired. Please restart." |
| E005 | Handshake timeout | "Could not connect to server." |
| E006 | Sync timeout | "Connection too slow. Try a closer region." |
| E007 | Reconnect failed | "Connection lost." |
| E008 | Kicked by host | "Removed from session." |
| E009 | Server shutdown | "Server maintenance. Please try again." |
