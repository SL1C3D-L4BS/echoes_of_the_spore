# Zone 08 -- Necrospore Core

## Overview

| Property | Value |
|---|---|
| Biome | Heart of the Dead Network |
| Lock Ability | ALL prior abilities required |
| Color Temperature | Necrospore Gold (#C9A84C) + void black (#0A0A0F) |
| Music Key | Polymodal, 160 BPM building to silence, full orchestra + electronic + choir |
| Room Count Range | 30-40 |
| Atmosphere | Transcendence, cosmic horror, finality |
| Estimated Clear Time | 25-45 min |
| Fossil Mural Count | 3 (Progenitor endgame -- the truth about the Mycelium's creation) |

## Narrative Role

The Necrospore Core is the endgame. This is the heart of the dead network -- the place where the Mycelium was born and where it will either be saved or consumed. The Progenitor murals here reveal the final truth: the Mycelium was not a natural organism but a creation, a tool built to preserve civilizations by absorbing them. Every civilization the player has encountered was consumed by design. The Mycelium Heart boss is the system's core intelligence, and it speaks to the player directly, arguing for its own survival.

## AI Director Specification

The Necrospore Core does not use static encounter design. An AI Director system dynamically tunes every room based on real-time party state.

### Director Input Parameters

| Parameter | Source | Update Frequency |
|---|---|---|
| Party Size | Session data | On room entry |
| Average Party HP % | Player stats | Every 5s |
| Average Infection Level | Player stats | Every 5s |
| Boon Loadout Composition | Inventory data | On room entry |
| Deaths This Run | Session counter | On death event |
| Time in Zone | Session timer | Continuous |
| Rooms Cleared | Session counter | On room clear |
| Recent DPS Output | Combat log (30s window) | Every 5s |
| Damage Taken Rate | Combat log (30s window) | Every 5s |

### Director Output Tuning

| Output | Range | Tuning Logic |
|---|---|---|
| Enemy Count | 4-20 per room | Base 8. +2 per player above 1. -2 if avg HP < 40%. +3 if recent DPS > threshold. |
| Enemy Tier Distribution | Tier 1-3 | Base: 40% T2, 60% T3. Shifts to 60% T2, 40% T3 if deaths > 3. |
| Hazard Selection | All zone hazards | Selects 2-4 hazards per room from all 8 zones. Avoids repeating the same hazard in consecutive rooms. Weights hazards from zones the party struggled with. |
| Hazard Intensity | 50-150% base values | Scales with party performance. High DPS output triggers higher intensity. |
| Room Size | 40x24 to 80x40 | Larger rooms for struggling parties (more space to maneuver). Smaller rooms for dominant parties (higher pressure). |
| Loot Quality | Common to Legendary | Inversely proportional to performance -- struggling parties get better drops. |
| Boss Prep Rooms | 0-2 | Inserts additional rest/reward rooms before boss if avg HP < 50% or deaths > 4. |

### Director Behavioral Modes

| Mode | Trigger | Behavior |
|---|---|---|
| Standard | Default | Balanced tuning, responsive to party state |
| Pressure | Party avg HP > 80%, 0 deaths, fast clear times | Increases enemy count +30%, hazard intensity +25%, shrinks rooms |
| Mercy | 3+ deaths, avg HP < 35% | Reduces enemy count -30%, increases loot quality, adds rest rooms |
| Escalation | Time in zone > 30 min | Gradual difficulty increase regardless of performance. Prevents infinite farming. |
| Finale | Rooms cleared > 28 (approaching boss) | Dramatic difficulty spike followed by a rest room. The calm before the storm. |

### Director Constraints

- **No Unfair Combos:** The Director cannot combine more than 2 high-intensity hazards in the same room (e.g., no pH Storm + Swarm Event + Reality Fracture simultaneously).
- **Hazard Cooldown:** Each specific hazard type has a 3-room cooldown after use.
- **Minimum Resources:** Every 5 rooms must include at least 1 rest or reward room, regardless of performance.
- **Boss Guarantee:** The boss room is always preceded by at least 1 non-combat room.
- **Transparency:** The Director's current mode is never displayed to the player. Tuning must feel organic, not mechanical.

## Room Type Distribution (Director-Modified)

| Room Type | Min | Max | Notes |
|---|---|---|---|
| Combat | 12 | 20 | Director controls enemy composition per room |
| Puzzle | 3 | 6 | Multi-ability puzzles requiring all unlocked abilities |
| Reward | 4 | 6 | Director adjusts loot quality based on performance |
| Rest | 3 | 5 | Director inserts additional rest rooms for struggling parties |
| Boss | 1 | 1 | Mycelium Heart arena -- fixed, not Director-tuned |
| Transition | 4 | 6 | Director uses these for pacing adjustments |
| Secret | 1 | 3 | Progenitor truth chambers; contain endgame lore |

## Enemy Roster

All enemies from previous zones can appear, plus the following Core-exclusive enemies:

| Enemy | Tier | HP | Damage | Behavior |
|---|---|---|---|---|
| Core Sentinel | 3 | 200 | 15 (dual blade), 10 (pulse) | Elite melee. Dual-wields crystallized Necrospore blades. 5-hit combo with tight dodge windows (0.4s between strikes). |
| Spore Nexus | 3 | 120 | 0 (buff) | Stationary. Buffs all enemies in room (+20% damage, +15% speed). Must be destroyed first. Regenerates if not killed within 10s of taking damage. |
| Progenitor Echo | 3 | 250 | 18 (reality beam) | Rare. Ancient construct. Fires a sweeping beam that covers 180 degrees. 2s charge, 3s sweep. Drops guaranteed Rare+ loot. |
| Necrospore Tendril | 3 | 80 (per segment) | 8 (grab per segment) | Multi-segment enemy. 4-6 segments that must be destroyed individually. Grabs and pulls players toward the Core. |
| Void Mimic | 3 | 150 | 12 (surprise attack) | Disguised as a loot chest or Forge Station. Reveals on interaction. First hit is guaranteed (surprise). Drops real loot on death. |

### Mini-Boss: Core Guardian (guaranteed, not random)

| Property | Value |
|---|---|
| Tier | 3 |
| HP | 800 |
| Damage | 22 (slam), 15 (AoE pulse), 30 (core beam -- avoidable) |
| Behavior | Appears at room 20-25. Two-phase fight: Phase 1 is a standard combat. Phase 2 activates a Synapse Puzzle that must be solved while dodging attacks. Guaranteed drop: Progenitor Key Fragment (required for boss access). |
| Spawn | Guaranteed once per run |

## Boss: The Mycelium Heart

The final boss. A multi-phase encounter that tests every skill learned across all 8 zones.

### Arena

Circular chamber, 60-tile diameter. Central pulsing organ (the Heart). Four conduit pillars at cardinal points connect to the Heart. Void surrounds the platform -- falling is instant death (respawn at edge after 3s, 20% HP penalty).

### Phase Table

| Phase | HP Pool | Zone Callback | Mechanics |
|---|---|---|---|
| Phase 1: Awakening (100-75%) | 1200 | Sporehaven | Heart spawns Puffcaps and Spore Clouds. Tutorial-level difficulty. The Heart speaks: "You came. They all come." Teaches players the conduit mechanic -- destroying a conduit pillar removes 25% of the Heart's shield. |
| Phase 2: Memory (75-50%) | 900 | Cortex + Fermentation | Synapse Puzzle nodes appear on the arena floor (must solve to expose Heart). pH zones create acid/alkaline quadrants. The Heart speaks: "I remember every one of them. The Luminari. The Ferrox. I remember their songs." |
| Phase 3: Fury (50-25%) | 750 | Chitinhall + Myco-Forge | Swarm Event with Core Drones. Forge Stations appear (one-use, free). Conveyor belts activate on the arena floor. The Heart speaks: "You break what you do not understand. Like the Pyreth. Like all builders." |
| Phase 4: Grief (25-10%) | 450 | Rot Garden + Necroveil | Entropy Fields expand from edges. Mycelium Hunters enter (2, on 30s patrol loops). Decay Timer activates (150s to Collapsed). The Heart speaks: "The Glacine stayed. They understood. Will you?" |
| Phase 5: Truth (10-0%) | 300 | All zones | All hazards simultaneously at low intensity. The Heart stops attacking. It speaks the Progenitor truth -- the player must choose to destroy it while it talks. If the player waits 60s without attacking, alternate ending triggers. |

### Heart Dialogue System

The Heart speaks directly to the player via subtitle text and processed vocal audio. Lines are contextual:

| Trigger | Line Category | Example |
|---|---|---|
| Phase transition | Lore revelation | "I was made to preserve. They called it salvation." |
| Player death | Acknowledgment | "You return. You always return. That is what I was built for." |
| High Infection (70+) | Recognition | "You feel it now. The network. We are not so different." |
| Low Infection (<20) | Challenge | "You resist. The Umbral resisted too. I remember how that ended." |
| Co-op (4 players) | Collective address | "Four minds. The Progenitors sent four as well. It was not enough." |
| Phase 5 (60s no attack) | Alternate ending | "You chose to listen. No one has listened since the Progenitors." |

## Hazard List (Director-Selected Pool)

All hazards from Zones 1-7 are available. The Director selects 2-4 per room from:

| Source Zone | Hazard | Base Effect |
|---|---|---|
| Sporehaven | Spore Cloud | Vision -40%, 5s |
| Cortex | Thought Parasite Field | Input reversal, 3s |
| Fermentation | pH Storm | Room pH drops to 1.5, 8s |
| Chitinhall | Swarm Event | 20-30 Drones, 15s |
| Rot Garden | Entropy Field | 1% stat drain/s |
| Myco-Forge | Pressure Lock | Timed crushing door |
| Necroveil | Reality Fracture | Random room teleport |
| Core-exclusive | Void Collapse | Platform section falls away for 10s, returns |

## Art Direction

**Palette:** Necrospore Gold (#C9A84C) for the Heart, conduits, and all Progenitor artifacts -- this is the color of truth. Void black (#0A0A0F) for everything else. The contrast should be stark and absolute. No intermediate colors except during hazard activations.

**Architecture:** The Core is a cathedral of organic machinery. The Heart is visible from every room, pulsing in the distance, growing larger as players progress. Walls are translucent membranes showing the network's root structure. The floor is a living surface that responds to footsteps with bioluminescent ripples.

**Scale:** Everything is enormous. Doorways are 5x normal height. Corridors are wide enough for 4 players abreast. The Heart arena is the largest space in the game. This is the Mycelium's true body, and the player is very small within it.

**Audio:** The score builds across the entire zone. Room 1: solo electronic pulse. Every 5 rooms: a new instrument layer adds. By the boss arena, the full orchestra + electronic + choir is active. Phase 5 of the boss: everything cuts to silence except the Heart's voice and a single sustained note.

## Level Design Guidelines

1. **Director Transparency:** The AI Director must never feel like rubber-banding. Difficulty adjustments should be gradual (2-3 rooms to take effect) and expressed through enemy composition, not stat inflation.
2. **All-Ability Gates:** At least 3 rooms per run must require specific abilities from earlier zones to progress. This validates the player's full journey.
3. **Progenitor Murals:** The 3 endgame murals reveal: (a) the Progenitors created the Mycelium as a preservation engine, (b) it worked too well -- it consumed rather than preserved, (c) the Progenitors entered the Core to shut it down and never returned. These murals must be encountered in order.
4. **Boss Preparation:** The final 3 rooms before the Heart should be Director-mode Finale: a difficulty spike, then a rest room with a Progenitor mural, then the boss door.
5. **No Farming:** The Escalation mode prevents players from staying in the zone indefinitely. After 40 minutes, difficulty becomes functionally impossible.
6. **Alternate Ending Access:** Phase 5's 60s wait window must feel intentional, not accidental. The Heart's dialogue should make it clear that not attacking is a choice, not inaction.
7. **Co-op Scaling:** The Heart's HP scales 1.5x per additional player. Phase 2's puzzle requires all players to stand on nodes simultaneously. Phase 4's Hunters target different players.
