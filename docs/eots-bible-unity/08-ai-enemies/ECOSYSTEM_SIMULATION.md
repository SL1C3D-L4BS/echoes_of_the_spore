# Ecosystem Simulation

## Overview

Enemies exist within a living food chain inspired by Rain World. Tier 1 creatures feed on environmental resources. Tier 2 creatures hunt Tier 1. Tier 3 creatures hunt Tier 2. This simulation runs retroactively when the player is absent, creating emergent room states.

## Predation Chain

```
[Environmental Resources] ← [Tier 1: Sporelings] ← [Tier 2: Predators] ← [Tier 3: Apex]
                                                                              ↑
                                                              [Mycelium Hunters] (Zone 7 only, hunt everything)
```

## Simulation Rules

### Active Simulation (Player Present)

When the player is in a room, enemies behave in real-time:

| Behavior | Rule |
|---|---|
| Tier 1 feeding | Tier 1 wanders toward fungal nodes, "feeds" for 3s (idle animation) |
| Tier 2 hunting Tier 1 | If Tier 2 hunger > 50, targets nearest Tier 1 via utility scoring |
| Tier 2 kill | Tier 2 deals lethal damage to Tier 1 in 1-2 hits. Feeds for 3s. |
| Tier 3 hunting Tier 2 | Tier 3 actively pursues Tier 2 within its room territory |
| Tier 3 kill | Tier 3 deals lethal damage to Tier 2 in 2-3 hits. No feed animation. |
| Player priority | All tiers prioritize player over ecosystem behavior when player is detected |

### Retroactive Simulation (Player Absent)

When the player is not in a room, time passes retroactively. On room entry, the game fast-forwards the ecosystem:

| Time Since Last Visit | Tier 1 Effect | Tier 2 Effect | Tier 3 Effect |
|---|---|---|---|
| 0-5 min | No change | No change | No change |
| 5-10 min | -10% Tier 1 pop (Tier 2 feeding) | No change | No change |
| 10-20 min | -30% Tier 1 pop | No change | 5% spawn chance |
| 20-40 min | -60% Tier 1 pop | -20% Tier 2 pop (Tier 3) | 10% spawn chance |
| 40-60 min | -80% Tier 1 pop | -40% Tier 2 pop | 15% spawn chance |
| 60+ min | -90% Tier 1 pop (min 1) | -50% Tier 2 pop (min 1) | 20% spawn chance |

### Calculation on Room Entry

```
time_elapsed = current_time - last_visit_time
tier1_surviving = ceil(tier1_original * survival_rate(time_elapsed))
tier2_surviving = ceil(tier2_original * survival_rate(time_elapsed))
tier3_spawned = random() < spawn_chance(time_elapsed)
```

Surviving creatures are placed at random valid spawn points within the room.

## Respawn Rules

| Rule | Detail |
|---|---|
| Tier 1 respawn | On room re-entry: 50% of original population, randomized from zone pool |
| Tier 2 respawn | On room re-entry: 50% of original population (not affected by ecosystem kills) |
| Tier 3 respawn | Does not respawn once killed. New roll on next visit cycle (5% per unvisited hour). |
| Respawn cooldown | Respawn count resets only after player has been absent for 5+ minutes |
| Killed vs ecosystem-killed | Both reduce population equally for respawn calculation |

## Environmental Resources (Fungal Nodes)

| Property | Value |
|---|---|
| Count per room | 3-8 |
| Respawn rate | 1 per 5 minutes of room being unvisited |
| HP | 1 (destroyed by any attack) |
| Loot (player) | 1 Common Silk |
| Loot (Tier 1) | Restores Tier 1 to full HP, no drop |
| Effect on ecosystem | If all nodes consumed, Tier 1 wanders more aggressively (larger patrol radius) |

## Population Caps

| Tier | Per Room Cap | Per Zone Cap |
|---|---|---|
| Tier 1 | 15 | 100 |
| Tier 2 | 3 | 20 |
| Tier 3 | 1 | 2 |
| Hunters (Zone 7) | N/A (fixed spawn) | 3-5 (fixed) |

## Ecosystem States (Per Room)

Each room can be in one of four ecosystem states based on its population:

| State | Condition | Player Experience |
|---|---|---|
| Teeming | >80% Tier 1, Tier 2 present | Lots of fodder, resources, moderate threat |
| Balanced | 40-80% Tier 1, Tier 2 present | Standard difficulty |
| Depleted | <40% Tier 1, Tier 2 present | Fewer resources, Tier 2 is hungry and aggressive |
| Apex Domain | Tier 3 present | Tier 2 absent/fleeing, Tier 1 scattered, high danger |

Room state affects ambient audio, lighting intensity, and particle density.

## Cross-Room Effects

| Effect | Detail |
|---|---|
| Tier 2 migration | If a room is depleted, Tier 2 may migrate to adjacent room (10% chance per 10 min) |
| Tier 3 roaming | Tier 3 may move to adjacent room if no Tier 2 prey remains (20% chance per 20 min) |
| Noise attraction | Combat noise in adjacent rooms draws Tier 2 to investigate (if noise > 30) |
| Flee cascading | Tier 3 spawn causes Tier 2 to flee to adjacent rooms |

## Multiplayer Impact

| Scenario | Effect |
|---|---|
| Multiple rooms occupied | Each room simulates independently. No retroactive sim for occupied rooms. |
| Fast clearing | Rapid room clears prevent ecosystem from recovering. Later rooms may be depleted. |
| Ignoring rooms | Rooms left alone for long periods become Apex Domains (fewer resources, higher danger). |

## Session Persistence

- Ecosystem state persists for the entire session (delve)
- Stored per-room in Redis: `session:{id}/room_states/{room_id}/ecosystem`
- On session end, ecosystem state is discarded (fresh on next delve)
- Ecosystem is deterministic given the same seed and time deltas (replay-compatible)

## Implementation Notes

- Retroactive simulation runs in `EcosystemSimJob` (Burst-compiled IJob) on room entry
- Population stored as `NativeHashMap<EntityType, int>` per room
- Migration uses room adjacency graph (same as noise propagation)
- Fungal node respawn tracked via `last_consumed_tick` per node
- Ecosystem state enum drives ambient audio/visual parameters via `EcosystemAmbientController`
- Debug: ecosystem state visible in room tooltip on the minimap (dev builds only)
