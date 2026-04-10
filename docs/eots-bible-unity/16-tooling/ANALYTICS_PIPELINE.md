# Analytics Pipeline

## Architecture

```
Game Client
  → Telemetry SDK (batched, async)
  → HTTPS POST to ingest endpoint
  → API Gateway (rate limited)
  → Event queue (SQS / Pub-Sub)
  → Stream processor (Lambda / Cloud Functions)
  → Data warehouse (BigQuery / Redshift)
  → Dashboard (Grafana / Metabase)
```

## Event Schema

All events share a common envelope:

```json
{
    "event_type": "string",
    "timestamp": "ISO-8601",
    "session_id": "uuid",
    "player_id": "uuid",
    "build_version": "string",
    "platform": "string",
    "region": "string",
    "payload": {}
}
```

## Event Types

### Session Events

| Event | Trigger | Key Payload Fields |
|-------|---------|-------------------|
| `session_start` | Match begins | zone_id, heat_level, player_count, classes[] |
| `session_end` | Match ends | duration_s, outcome (clear/death/quit), echoes_earned |
| `session_crash` | Unhandled exception | stack_trace, frame_count |

### Gameplay Events

| Event | Trigger | Key Payload Fields |
|-------|---------|-------------------|
| `player_death` | Player dies | cause, zone, room, infection_level, hp_at_death |
| `boss_kill` | Boss defeated | boss_id, time_to_kill_s, player_hp_remaining[] |
| `boss_wipe` | Party wipes on boss | boss_id, boss_hp_remaining_pct, attempts |
| `boon_selected` | Boon chosen | boon_id, rarity, alternatives_offered[] |
| `boon_skipped` | Boon skipped | alternatives_offered[] |
| `infection_tier_change` | Tier boundary crossed | old_tier, new_tier, time_in_session_s |
| `fusion_merge` | Players fuse | partner_classes[], infection_levels[] |
| `fusion_separate` | Players separate | duration_fused_s, damage_dealt_while_fused |
| `spore_plan_execute` | Spore Plan used | plan_type, participants, damage_dealt |
| `echo_deposit` | Echoes contributed to tree | amount, tree_stage_before, tree_stage_after |

### Technical Events

| Event | Trigger | Key Payload Fields |
|-------|---------|-------------------|
| `desync` | Checksum mismatch | tick, client_hash, server_hash, rtt_ms |
| `rollback` | Rollback triggered | depth_ticks, duration_us |
| `frame_spike` | Frame > 33ms | frame_time_ms, ecs_time_ms, entity_count |
| `memory_warning` | Memory > 80% budget | used_mb, budget_mb, largest_allocation |
| `network_quality` | Every 30s | rtt_ms, packet_loss_pct, bandwidth_kbs |

### Economy Events

| Event | Trigger | Key Payload Fields |
|-------|---------|-------------------|
| `currency_earn` | Echoes gained | amount, source (run/tree/achievement) |
| `currency_spend` | Echoes spent | amount, item_type, item_id |
| `battle_pass_progress` | Pass tier gained | tier_reached, xp_earned |

## Batch Configuration

| Parameter | Value | Notes |
|-----------|-------|-------|
| Batch size | 50 events or 30s | Whichever comes first |
| Max batch payload | 64 KB | Compress if exceeded |
| Retry policy | 3 attempts, exponential backoff | 1s, 4s, 16s |
| Offline queue | 500 events | Persist to disk, send on reconnect |
| Compression | gzip | For batches > 8 KB |
| Endpoint | `https://telemetry.eots.sliced-labs.com/v1/events` | — |

## Privacy and Compliance

| Requirement | Implementation |
|------------|---------------|
| GDPR consent | Opt-in telemetry toggle in settings |
| Data minimization | No PII in events (player_id is opaque UUID) |
| Retention | 90 days hot, 2 years cold storage |
| Right to delete | Delete by player_id across all stores |
| Age gating | No telemetry for players under 13 (COPPA) |
| Regional | EU data stays in EU region |

## Key Dashboards

| Dashboard | Metrics | Refresh |
|-----------|---------|---------|
| Live Operations | CCU, match queue depth, server health | Real-time |
| Game Health | Crash rate, desync rate, avg session length | Hourly |
| Balance | Boss kill rates, class pick rates, boon selection | Daily |
| Retention | DAU, D1/D7/D30, session count distribution | Daily |
| Economy | Echoes earn/spend rate, battle pass progression | Daily |
| Performance | Frame time P95, memory usage, load times | Hourly |
| Funnel | New player → first run → first boss → first fusion | Daily |

## Key Metrics and Targets

| Metric | Target | Alert Threshold |
|--------|--------|----------------|
| D1 retention | > 40% | < 30% |
| D7 retention | > 20% | < 15% |
| D30 retention | > 10% | < 7% |
| Avg session length | 25-40 min | < 15 min |
| Sessions per DAU | 1.5-2.5 | < 1.2 |
| Boss kill rate (first attempt) | 15-25% | > 40% (too easy) or < 10% (too hard) |
| Crash rate | < 0.5% | > 1.0% |
| Desync rate | < 0.1% | > 0.5% |
| Matchmaking time (P95) | < 30s | > 60s |

## A/B Testing Framework

| Component | Detail |
|-----------|--------|
| Assignment | Deterministic hash of player_id + experiment_id |
| Max concurrent experiments | 5 |
| Min sample size | 1000 players per variant |
| Metrics tracked | Retention, session length, spend rate |
| Duration | 7-14 days minimum |
| Rollout | 10% → 50% → 100% over 3 days |
