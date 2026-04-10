# Matchmaking

## Overview

Matchmaking uses Redis sorted sets with a FIFO-first, skill-adjusted approach. The system prioritizes queue time to keep waits short, then applies skill-based filtering within acceptable bounds.

## Queue Architecture

```
[Client] → [API Gateway] → [Matchmaker Service] → [Redis Sorted Sets] → [Session Server]
```

### Redis Data Structures

| Key | Type | Contents |
|---|---|---|
| `queue:pve:{zone}` | Sorted Set | member=player_id, score=queue_entry_timestamp |
| `queue:rite` | Sorted Set | member=player_id, score=queue_entry_timestamp |
| `player:{id}:mmr` | String | PvE skill rating (0-3000) |
| `player:{id}:rite_elo` | String | Rite Elo rating (initial 1000) |
| `player:{id}:party` | Set | party member player_ids |
| `player:{id}:platform` | String | pc, xbox, ps5, switch |
| `player:{id}:input` | String | kbm, controller |
| `party:{id}:members` | List | ordered player_ids |

## PvE Matchmaking Flow

1. Player (or party leader) queues for a zone
2. Entry added to `queue:pve:{zone}` with timestamp score
3. Matchmaker polls queue every 500ms
4. For each candidate (FIFO order):
   a. Find compatible players within MMR range (initial: +/- 200)
   b. Check platform/input compatibility (see Cross-Play rules)
   c. Check party size compatibility (sum must not exceed 4)
   d. If match found: create session, remove all matched players from queue
   e. If no match after 30s: expand MMR range by 100 (max expansion: 500)
   f. If no match after 120s: match with any available player in zone queue

## Skill-Based Matching

### PvE MMR

| Component | Weight |
|---|---|
| Zone completion rate | 40% |
| Average clear time | 30% |
| Death rate per run | 20% |
| Boss kill contribution | 10% |

- MMR range: 0-3000
- Initial MMR: 1000
- Recalculated after every completed delve
- Decay: -10 MMR per week of inactivity (floor: 500)

### Rite Elo

Standard Elo with K=32, initial 1000. See RITE_OF_ECHOES.md for details.

## Party System

| Parameter | Value |
|---|---|
| Max party size | 4 |
| Party persistence | Until leader disbands or all members offline for 10 min |
| Cross-platform parties | Allowed |
| Party MMR | Mean of all member MMRs |
| Party queue priority | +0 (no advantage or penalty) |

### Party Formation

1. Leader sends invite via friend list or invite code (6-char alphanumeric, 5-min TTL)
2. Invitees accept/decline
3. Party stored in Redis set `party:{leader_id}:members`
4. Party queues as a unit; matchmaker treats party as a single block

## Backfill Rules

| Rule | Value |
|---|---|
| Backfill enabled | During ACTIVE state only |
| Backfill window | First 50% of zone rooms cleared |
| Backfill MMR range | +/- 300 from session average |
| Backfill priority | Players who recently disconnected from same session > new players |
| Max backfill per session | 2 players |
| Backfill cooldown | 60s between backfill attempts |

## Cross-Play Matching

| Platform Group | Members | Default Pool |
|---|---|---|
| PC-KBM | PC with keyboard/mouse | PC pool |
| PC-Controller | PC with gamepad | Console pool |
| Console | Xbox, PS5, Switch | Console pool |

- Cross-play toggle: ON by default, can be disabled in settings
- When cross-play OFF: match only within same platform
- Input-based override: PC-Controller players join Console pool by default
- Party cross-play: if any party member has cross-play OFF, entire party matches within most restrictive pool

## Queue Diagnostics

| Metric | Tracked |
|---|---|
| Average queue time | Per zone, per region, rolling 5-min window |
| Queue depth | Current players waiting per zone |
| Match quality | MMR spread of formed sessions |
| Backfill rate | % of sessions that required backfill |
| Abandon rate | % of players who left queue before matching |

## Error Handling

| Scenario | Response |
|---|---|
| Player disconnects during queue | Remove from sorted set. No penalty. |
| Party member disconnects | Party remains in queue if 1+ members remain. |
| Matchmaker service crash | Queued players retained in Redis. Service restart resumes processing. |
| Session server full | Queue player with `retry_after=5s` flag. |

## Implementation Notes

- Matchmaker runs as a standalone .NET service (not inside Unity)
- Redis operations use Lua scripts for atomic match-and-remove
- Queue polling interval: 500ms (configurable per region load)
- Metrics exported to Prometheus via Redis keyspace notifications
- Rate limit: 1 queue join per player per 5 seconds (prevents spam)
