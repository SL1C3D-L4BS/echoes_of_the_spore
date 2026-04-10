# Spectator Mode

## Overview

Spectator mode allows non-playing viewers to watch live sessions. Spectators receive
the full game state but send no inputs. Used for tournaments, streaming, and debugging.

## Architecture

```
Active Session (4 players + server)
  Server ──StateStream──→ Spectator Client(s)
  
Spectator receives:
  - Full world state snapshots (every 15 ticks / 250ms)
  - All player inputs (for interpolation)
  - Voice audio (optional, privacy-gated)
  - No authority to send InputMsg
```

## Spectator Limits

| Parameter | Value | Notes |
|-----------|-------|-------|
| Max spectators per session | 8 | Bandwidth limited |
| Spectator snapshot rate | 4 Hz (every 15 ticks) | Lower than player rate |
| Spectator input delay | 500ms (30 ticks) | Intentional delay to prevent cheating |
| Voice relay | Opt-in per player | Privacy: players choose if spectators hear voice |
| State size per snapshot | ~2-4 KB compressed | Full world state |

## Bandwidth Per Spectator

| Data Stream | Rate | Size | Bandwidth |
|-------------|------|------|-----------|
| State snapshots | 4/s | 3 KB avg | 12 KB/s |
| Input relay | 60/s | 42 B | 2.5 KB/s |
| Voice (if enabled) | Continuous | 3 KB/s per player | 0-12 KB/s |
| **Total** | — | — | **14.5-26.5 KB/s** |

## Camera Modes

| Mode | Binding | Behavior |
|------|---------|----------|
| Follow Player | 1-4 keys | Camera tracks selected player |
| Free Cam | F | WASD camera movement, independent |
| Auto Director | A | AI-driven camera, follows action |
| Overview | O | Zoomed-out view of current room |
| Cycle | Tab | Cycle through players |

## Auto Director Logic

```
Priority score per player (evaluated every 2s):
  + 100  if in combat (enemies within 10 units)
  + 200  if fighting boss
  + 50   if infection tier changed in last 10s
  + 150  if fusing/separating
  + 75   if HP < 25%
  + 30   if moving fast (dash/ability)
  - 50   if idle for > 5s

Camera switches to highest-priority player.
Minimum dwell time: 5s (no rapid switching).
Transition: 1.0s smooth pan.
```

## HUD Overlay for Spectators

| Element | Position | Content |
|---------|----------|---------|
| Player info bars (x4) | Top | Name, HP, Infection %, Class icon |
| Current room/zone | Top-center | Zone name + room index |
| Kill feed | Right | Recent kills with damage source |
| Timer | Top-right | Session duration |
| Spectator count | Bottom-right | "Watching: N" |
| Camera mode indicator | Bottom-left | Current mode name |

## Privacy Controls

| Setting | Default | Player Control |
|---------|---------|---------------|
| Allow spectators | On | Session host toggle |
| Voice audible to spectators | Off | Per-player opt-in |
| Spectator delay | 500ms | Not configurable (anti-cheat) |
| Spectator chat | Off | Host toggle |
| Show spectator names to players | Off | Host toggle |

## Spectator Join Flow

```
1. Spectator selects "Watch" from friends list or session browser
2. Client sends SpectateRequest to matchmaking service
3. Matchmaking validates: session allows spectators, count < 8
4. Matchmaking returns server address + spectator token
5. Spectator connects with spectator token (no InputMsg rights)
6. Server adds spectator to broadcast list
7. Server sends initial full StateMsg
8. Spectator begins rendering from state + input stream
```

## Spectator Message Types

| Message | Direction | Content |
|---------|-----------|---------|
| SpectateRequest | Spectator → Matchmaker | session_id, spectator_id |
| SpectateAccept | Matchmaker → Spectator | server_addr, token |
| SpectateJoin | Spectator → Server | token |
| StateStream | Server → Spectator | Periodic full state |
| InputStream | Server → Spectator | All player inputs |
| VoiceStream | Server → Spectator | Opted-in voice data |
| SpectateLeave | Spectator → Server | Disconnect |

## Tournament Mode Extensions

| Feature | Specification |
|---------|--------------|
| Broadcast delay | 30s (configurable up to 120s) |
| Caster audio channel | Separate VOIP channel for commentators |
| Overlay API | WebSocket endpoint for OBS/streaming overlays |
| Replay markers | Casters can mark moments for instant replay |
| Stats feed | JSON endpoint: DPS, damage taken, infection rate, etc. |

## Performance Impact on Server

| Spectator Count | Additional Server CPU | Additional Bandwidth |
|----------------|----------------------|---------------------|
| 1 | +2% | +15 KB/s |
| 4 | +5% | +60 KB/s |
| 8 | +8% | +120 KB/s |
