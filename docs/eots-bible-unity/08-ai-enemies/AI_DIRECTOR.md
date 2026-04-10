# AI Director -- Zone 8 Only

## Overview

The Necrospore Core (Zone 8) uses a real-time AI Director that dynamically adjusts difficulty to maintain engagement. It reads party performance metrics and outputs enemy composition, hazard intensity, boon quality, and boss pacing. The Director targets a 30-40% room failure rate.

## Scope

- Active only in Zone 8 (Necrospore Core)
- Begins influencing the session from the first room of Zone 8
- Escalates intensity toward the Mycelium Heart boss fight
- During the boss fight, directly controls Phase 3 attack selection

## Input Signals

### Primary Inputs

| Input | Source | Range | Update Rate |
|---|---|---|---|
| Party avg Infection | Mean of all player Infection levels | 0-100 | Per room entry |
| Boon loadout | Set of active boons across all players | Categorical | Per room entry |
| Recent death rate | Deaths in rolling 5-minute window | 0-20 | Per tick |
| Heat level | Current Pact heat (player-chosen difficulty) | 0-100 | Session start |
| Time in current room | Seconds since room entry | 0-600 | Per tick |
| Room clear streak | Consecutive rooms cleared without failure | 0-N | Per room clear |

### Secondary Inputs

| Input | Source | Purpose |
|---|---|---|
| Avg player HP % | Mean HP across party | Detect struggling parties |
| Healing resource count | Total heals remaining across party | Predict sustainability |
| Spore Gauge fill rate | How fast gauge fills (proxy for combat efficiency) | Detect skilled play |
| Fusion usage rate | % of time spent fused vs individual | Identify party strategy |

## Output Controls

### 1. Enemy Composition

| Scenario | Director Response |
|---|---|
| Party has strong AoE boons | Spawn fewer swarm enemies; more single-target elites |
| Party has strong single-target | Spawn more adds and swarms to overwhelm |
| Party uses ranged builds | Spawn Myco-Lurkers (ambush) and Chitin Knights (close gap) |
| Party is melee-heavy | Spawn Bloom Snipers (ranged) and Rot Drifters (area denial) |
| High Infection party | Spawn Spore Shamans (Infection damage amplified) |

Selection uses a counter-matrix:

| Player Strength | Counter Enemy Pool |
|---|---|
| Fire damage | Acid-resistant enemies (Distillery variants) |
| Crowd control | CC-immune elites |
| High mobility | Area denial enemies (Puffcap, Acid Sprout) |
| Healing/sustain | Infection-heavy enemies (Spore Shaman, Rot Drifter) |
| Stealth play | Noise-generating enemies that reveal position |

### 2. Hazard Intensity

| Death Rate (5 min) | Hazard Scale |
|---|---|
| 0 deaths | 1.5x (complacent party gets pushed) |
| 1 death | 1.2x |
| 2 deaths | 1.0x (baseline) |
| 3 deaths | 0.8x |
| 4+ deaths | 0.6x (struggling party gets relief) |

Hazard types affected: environmental damage frequency, trap density, area denial coverage.

### 3. Boon Offering Quality

| Room Clear Streak | Boon Quality Modifier |
|---|---|
| 0-1 rooms | Standard pool |
| 2-3 rooms | +10% chance of Tier 2 boon |
| 4-5 rooms | +25% chance of Tier 2 boon |
| 6+ rooms | +10% chance of Tier 3 boon (rare) |

After a failure (party wipe):
- Next boon offering guaranteed Tier 2 minimum
- Healing resource drop rate +50% for 2 rooms

### 4. Boss Phase Transition Speed

| Party Skill Proxy | Phase Duration Modifier |
|---|---|
| High skill (0-1 deaths, fast clears) | 0.8x (faster transitions, less time to learn) |
| Average skill (2-3 deaths) | 1.0x (baseline) |
| Low skill (4+ deaths) | 1.3x (more time per phase, more learning opportunity) |

Skill proxy = composite of death rate + clear time + damage taken.

## Intensity Curve

The Director follows a macro-level intensity curve across Zone 8:

```
Room 1-3:  Ramp-up (0.7x intensity)
Room 4-6:  Standard (1.0x intensity)
Room 7-9:  Escalation (1.2x intensity)
Room 10+:  Pre-boss (1.4x intensity, fewer boons, tense)
Boss:      Director-controlled Phase 3
```

## Rubber-Banding

The Director enforces rubber-banding to prevent both steamrolling and brick-walling:

| Condition | Adjustment |
|---|---|
| 3+ rooms cleared with 0 deaths | Spike room: +50% enemy count, +1 elite |
| 3+ consecutive failures | Relief room: -30% enemies, guaranteed heal drop |
| All players below 30% HP entering a room | Reduced enemy aggression for first 10s (grace period) |
| Total session deaths > 15 | Director caps intensity at 0.8x for remainder |

## Target Metrics

| Metric | Target | Acceptable Range |
|---|---|---|
| Room failure rate | 35% | 30-40% |
| Session completion rate | 25% | 20-30% |
| Average session length | 45 min | 35-55 min |
| Boss encounter rate | 80% of sessions reach the boss | 75-85% |

## Data Logging

Every Director decision is logged for tuning:

| Field | Content |
|---|---|
| Session ID | Unique session identifier |
| Room ID | Which room the decision applies to |
| Inputs snapshot | All input values at decision time |
| Output decision | Enemy composition, hazard scale, boon modifier |
| Outcome | Room cleared/failed, time, deaths, damage dealt/taken |

Logs shipped to analytics pipeline for offline analysis and Director weight tuning.

## Boss Fight (Mycelium Heart Phase 3)

During Phase 3, the Director controls:

| Control | Behavior |
|---|---|
| Attack selection | Chooses attacks that counter each player's strongest defense |
| Attack timing | Increases tempo against high-skill parties, slows against struggling parties |
| Personalized VO | Selects Mycelium Voice lines that reference each player's class and playstyle |
| Absorption target | Targets the player with highest remaining HP (force team decision) |

## Implementation Notes

- Director implemented as `AIDirectorSystem` (ECS system, runs per room transition + per tick during boss)
- Input signals read from `PartyStatsBuffer` (aggregated each tick from player components)
- Counter-matrix stored as `NativeHashMap<BoonCategory, EnemyPool>` loaded from ScriptableObject
- Intensity curve defined as `AnimationCurve` on Director ScriptableObject (editable in inspector)
- Logging uses structured JSON output to a local buffer, flushed to S3 every 60s
- Tuning: all weights and thresholds in `DirectorConfig` ScriptableObject (hot-reloadable in dev builds)
- Director decisions are deterministic given inputs (replay-compatible for debugging)
