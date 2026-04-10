# Zone 04 -- Chitinhall

## Overview

| Property | Value |
|---|---|
| Biome | Insectoid Hive Architecture |
| Lock Ability | Carapace Shift (alternate path from Cortex) |
| Color Temperature | Warm amber (#DAA520) + cold grey (#708090) |
| Music Key | E minor, 130 BPM, staccato strings + rhythmic clicking |
| Room Count Range | 25-40 |
| Atmosphere | Claustrophobia, buzzing tension, organized menace |
| Estimated Clear Time | 15-22 min |
| Fossil Mural Count | 3 (Verdant civilization) |

## Narrative Role

Chitinhall is the Mycelium's immune system -- an insectoid hive that responds to intrusion with overwhelming collective force. The Verdant civilization once lived symbiotically with the swarm, acting as shepherds who directed hive behavior through pheromone language. Their murals depict a society that valued the collective over the individual, and their downfall came when the hive outgrew their control. The zone teaches players that brute force fails against numbers -- crowd control, stealth, and efficiency are the verbs that matter here.

## Swarm AI Behavior Specification

The hive operates as a unified organism. Individual enemies are expendable; the swarm's collective behavior is the real threat.

### Hive Alarm System

| Alarm Level | Trigger | Swarm Response | Decay Rate |
|---|---|---|---|
| 0 -- Dormant | Default state | Patrol routes only, passive | N/A |
| 1 -- Alert | Loud ability use, enemy death within 8 tiles of another | Patrol speed +50%, 5 additional Drones spawn at edges | -1 level per 20s of silence |
| 2 -- Agitated | 3+ enemy deaths in 10s, Spore-Talk use | Patrol speed +100%, Swarm Event triggers in 15s instead of 90s | -1 level per 30s of silence |
| 3 -- Hostile | Boss egg sac destroyed, 5+ deaths in 10s | Continuous spawning (2 Drones/s), all enemies gain +25% damage | -1 level per 45s of silence |

### Noise Values by Action

| Action | Noise Value | Alarm Contribution |
|---|---|---|
| Walking | 0 | None |
| Running | 1 | None (below threshold) |
| Light attack | 2 | +0.1 alarm per use |
| Heavy attack | 4 | +0.3 alarm per use |
| Ability use | 3-6 (varies) | +0.2 to +0.5 alarm |
| Spore-Talk (voice chat) | 5 | +0.4 alarm, flat |
| Enemy death | 3 | +0.2 alarm |
| Egg sac destruction | 8 | Instant Alarm Level 3 |

### Swarm Behavior Patterns

| Pattern | Description | Trigger Condition |
|---|---|---|
| Surround | Drones encircle player at 3-tile radius, close in unison | 6+ Drones targeting same player |
| Funnel | Swarm flows through narrow corridors in a stream | Room has chokepoint geometry |
| Shield Wall | Warriors form a line protecting a high-value target (egg sac, Queen) | Alarm Level 2+, near objective |
| Scatter | All units flee to edges, regroup in 5s | AoE ability hits 4+ units simultaneously |
| Kamikaze | Drones charge directly, exploding on contact (8 damage each) | Alarm Level 3, player HP > 70% |

## Room Type Distribution

| Room Type | Min | Max | Notes |
|---|---|---|---|
| Combat | 10 | 18 | Swarm density increases with room depth |
| Puzzle | 2 | 4 | Pheromone routing puzzles (redirect swarm paths) |
| Reward | 3 | 5 | AoE and crowd-control boons weighted 3x |
| Rest | 3 | 4 | Sealed chambers (alarm resets to 0 on entry) |
| Boss | 1 | 1 | Queen's chamber with egg sac ring |
| Transition | 4 | 6 | Hive tunnel corridors, Drone patrol routes visible |
| Secret | 0 | 2 | Behind breakable chitin walls; contain Verdant pheromone recipes |

## Enemy Roster

| Enemy | Tier | HP | Damage | Behavior |
|---|---|---|---|---|
| Hive Drone | 1 | 20 | 3 (bite) | Swarm unit. Spawns in groups of 4-8. Follows swarm behavior patterns. Dies in 1-2 hits. |
| Chitin Warrior | 2 | 110 | 9 (claw slash), 6 (charge) | Armored front (50% damage reduction). Must be flanked or hit from above. 1.2s charge wind-up. |
| Spore Wasp | 1 | 30 | 5 (sting), 4 (poison DoT 3s) | Flying. Erratic movement. Poison stacks up to 3x. Attracted to injured players. |
| Egg Tender | 2 | 90 | 7 (acid spit) | Stays near egg sacs. Heals egg sacs 5 HP/s. Destroying all Tenders in a room prevents egg sac respawns. |
| Brood Warden | 2 | 140 | 12 (slam), AoE pheromone buff | Commander unit. Buffs all Tier 1 enemies within 6 tiles (+30% speed, +20% damage). Kill priority. |

### Mini-Boss: Chitin Colossus

| Property | Value |
|---|---|
| Tier | 3 |
| HP | 500 |
| Damage | 18 (stomp AoE), 14 (charge) |
| Behavior | Massive armored unit. No weak spots -- must be worn down. Stomp spawns 6 Drones. Charge destroys terrain. Alarm instantly set to Level 2 on spawn. |
| Spawn Chance | 35% per run |

## Boss: The Hive Queen

| Phase | HP Pool | Mechanics |
|---|---|---|
| Phase 1 (100-70%) | 800 | Surrounded by 8 egg sacs (50 HP each). Spawns Drones continuously (1/2s). Queen is shielded until 4+ egg sacs destroyed. Ranged acid spit (10 damage). |
| Phase 2 (70-35%) | 560 | Queen becomes mobile. Alternates between charge attacks and summoning Chitin Warriors (2 per summon, 20s cooldown). Remaining egg sacs hatch elite Drones (40 HP, 6 damage). |
| Phase 3 (35-0%) | 280 | Queen anchors to ceiling. Rain of larval pods (3 damage each, 8 pods per wave, 10s interval). Players must destroy the anchor points (3 total, 100 HP each) to bring her down for damage windows. |

**Design Intent:** Tests crowd management and target prioritization. Players must balance clearing adds with dealing boss damage. The alarm system punishes reckless play.

## Hazard List

| Hazard | Effect | Duration | Telegraph |
|---|---|---|---|
| Swarm Event | 20-30 Tier 1 Drones flood room | 15s | Buzzing audio crescendo, 3s warning |
| Acid Floor Trap | 8 damage + slow 30% | 4s contact | Slightly darker floor tiles, faint hiss |
| Collapsing Chitin | Ceiling section falls, 20 damage in 2x2 tile area | Instant | Dust particles fall for 2s, cracking SFX |
| Pheromone Vent | Attracts all enemies to vent location for 8s | 8s | Green gas emission, 1.5s warning |

## Art Direction

**Palette:** Warm amber (#DAA520) for chitin surfaces and organic architecture. Cold grey (#708090) for stone and mineral deposits. Bioluminescent amber veins run through walls. Enemy highlights use warning red (#CC3333).

**Architecture:** Hexagonal geometry dominates. Rooms are honeycombed chambers connected by rounded tunnels. Egg sacs cluster on walls and ceilings, pulsing with internal movement. Floor surfaces are polished chitin with subtle iridescence.

**Animation:** Swarm movement uses flocking simulation -- Drones follow emergent patterns rather than scripted paths. Hive walls "breathe" at 0.3 Hz. Egg sacs use a translucent shader showing silhouetted larvae within.

**Audio:** Base clicking rhythm at 130 BPM using sampled insect sounds. Alarm level changes add layers: Level 1 adds violin tremolo, Level 2 adds brass stabs, Level 3 adds full percussion. Queen vocalizations use processed wasp recordings pitched down 2 octaves.

## Level Design Guidelines

1. **Chokepoint Strategy:** At least 50% of rooms must feature defensible chokepoints where players can funnel swarms. This is the zone's core tactical verb.
2. **Alarm Awareness:** The alarm level must be visible in the HUD at all times. Rooms should provide "silence windows" -- 15s gaps with no forced combat -- to allow alarm decay.
3. **Egg Sac Placement:** Egg sacs appear in clusters of 2-4. Destroying them is high-noise, high-reward. Place them where players must make a tactical decision about alarm cost.
4. **Vertical Threat:** At least 30% of rooms include ceiling-mounted enemies (Spore Wasps, Egg Tenders) to force upward awareness.
5. **Verdant Murals:** Placed in sealed rest rooms. Murals show the Verdant's pheromone language -- players who study them gain hints about swarm behavior patterns.
6. **Scaling Warning:** Room 1 of Chitinhall should have a "demonstration swarm" -- a visible but non-aggressive Drone column passing through a glass-walled corridor, showing players what they will face.
