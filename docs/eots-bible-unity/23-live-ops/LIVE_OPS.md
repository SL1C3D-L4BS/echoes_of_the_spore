# 23 — LIVE OPS, TELEMETRY & CONTENT SCALING

## Seasonal Content Calendar
| Season | Duration | Content Drop |
|---|---|---|
| Season 1 (Launch) | Months 1-3 | Base game + Battle Pass 1 |
| Season 2 | Months 4-6 | New Zone 9 (The Hollow Garden) + 2 new Fused Classes + Battle Pass 2 |
| Season 3 | Months 7-9 | New Civilization (9th) + new Duo Echoes + Battle Pass 3 |
| Season 4 | Months 10-12 | Rite of Echoes PvP launch + new Crests + Battle Pass 4 |

## Telemetry Events (Core)
| Event | Data Collected | Purpose |
|---|---|---|
| session_start | player_id, server_id, class, crest, heat_level | Population analytics |
| session_end | duration, outcome, echoes_earned, infection_end, rooms_cleared | Engagement metrics |
| boon_selected | boon_id, civilization, rarity, slot, alternatives_offered | Balance tuning |
| death | cause, enemy_id, room_id, tick, infection_at_death, HP_at_death | Difficulty tuning |
| fusion_event | type (merge/separate), partners, class_combo, duration | Fusion engagement |
| community_tree_contribute | amount, multiplier, node_id, server_id | Economy health |
| desync_detected | tick, client_checksum, server_checksum, player_count | Netcode health |
| purchase | item_id, price, currency, platform | Revenue analytics |

## Balance Tuning Process
1. Collect 1 week of telemetry data post-patch
2. Identify outliers: boons with >2x or <0.5x pick rate relative to alternatives
3. Identify outliers: classes/crests with >60% or <15% usage share
4. Identify outliers: enemies with >50% or <5% death cause share
5. Propose numerical adjustments (±10-20% per patch, no larger swings)
6. A/B test on 5% of population for 48 hours
7. Ship to 100% if metrics improve

## Server Lifecycle
- New servers spin up weekly during growth phase
- Servers with <100 DAU for 30 consecutive days are flagged for merge
- Merge process: combine Community Tree progress (take maximum of each node), transfer all player data
- Server merge notification: 7 days advance notice to players
- Archived servers: read-only access to Canopy state for nostalgia visits
