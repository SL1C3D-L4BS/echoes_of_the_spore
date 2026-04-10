# Zone 06 -- Myco-Forge

## Overview

| Property | Value |
|---|---|
| Biome | Biomechanical Foundry |
| Lock Ability | Spore Weld (from Fermentation Pits path) |
| Color Temperature | Hot orange (#FF6B35) + deep charcoal (#1A1A2E) |
| Music Key | C minor, 140 BPM, hammering percussion + brass stabs |
| Room Count Range | 20-35 |
| Atmosphere | Industry, purpose, heat, the Mycelium's workshop |
| Estimated Clear Time | 15-25 min |
| Fossil Mural Count | 3 (Pyreth civilization) |

## Narrative Role

The Myco-Forge is where the Mycelium builds. Raw materials from the Fermentation Pits arrive here to be shaped into the network's infrastructure -- walls, pathways, defenses. The Pyreth civilization were the Mycelium's engineers, a people who understood that creation requires destruction. Their murals depict forge-rituals where living matter was willingly melted and reformed. The Smelter boss is the last Pyreth forge-master, still working at a forge that serves a purpose no one remembers.

## Forge Upgrade System

The Forge is the zone's unique strategic layer. Players can spend Echoes at Forge Stations to temporarily enhance their equipment mid-run.

### Forge Station Mechanics

| Property | Value |
|---|---|
| Stations Per Run | 3-5 (placed in reward and transition rooms) |
| Currency | Echoes (standard run currency) |
| Persistence | Upgrades last until death, zone exit, or run completion |
| Stacking | Maximum 3 forge upgrades active simultaneously |
| Failure Risk | 15% chance of forge misfire (upgrade is weaker tier, Echoes still consumed) |

### Forge Upgrade Table

| Upgrade | Echo Cost | Effect | Tier |
|---|---|---|---|
| Tempered Edge | 50 | +15% weapon damage | Common |
| Molten Guard | 60 | +20% damage resistance | Common |
| Slag Coating | 45 | Attacks apply 3s burn (2 DPS) | Common |
| Pressure Core | 80 | +25% ability charge rate | Uncommon |
| Fusion Catalyst | 100 | Fusion abilities cost 20% less | Uncommon |
| Pyreth Alloy | 120 | +30% damage, +15% resistance, attacks cause knockback | Rare |
| Heart of the Forge | 200 | All stats +10%, immune to heat hazards, weapon trails with fire VFX | Legendary (1 per run max) |

### Forge Interaction Flow

1. Player approaches Forge Station (glowing anvil + bellows).
2. HUD displays available upgrades (randomized pool of 3 from the table above).
3. Player selects and pays Echoes.
4. 2s forging animation plays (player is vulnerable).
5. Upgrade applies with VFX confirmation (weapon glows, armor plates shift).
6. Misfire check: on failure, upgrade applies at one tier lower (Common becomes a minor stat buff).

### Forge Strategic Considerations

- Spending Echoes on forging reduces currency available for Boon purchases at the Canopy.
- Forge upgrades are lost on death, making them a high-risk investment for struggling players.
- Optimal play involves forging before the boss, not early in the zone.
- Co-op: Forge Stations have enough uses for the full party (4 uses per station).

## Room Type Distribution

| Room Type | Min | Max | Notes |
|---|---|---|---|
| Combat | 8 | 14 | Construct enemies dominate; heat hazards in 70% of rooms |
| Puzzle | 2 | 4 | Pressure lock sequences (timed platforming) |
| Reward | 3 | 5 | Forge Stations appear here; also standard loot |
| Rest | 2 | 3 | Cooling chambers with ambient forge lore |
| Boss | 1 | 1 | Smelter's crucible arena |
| Transition | 3 | 5 | Conveyor belt corridors with moving hazards |
| Secret | 0 | 2 | Behind weld-sealed doors (Spore Weld required); Pyreth schematics |

## Enemy Roster

| Enemy | Tier | HP | Damage | Behavior |
|---|---|---|---|---|
| Slag Drone | 2 | 80 | 7 (molten spit, ranged) | Hovering construct. Fires in 3-round bursts. Weak to cold-type abilities. |
| Forge Golem | 2 | 130 | 11 (hammer slam), 5 (heat aura 2-tile) | Slow, heavily armored. Heat aura deals passive damage. Staggered by 3 consecutive hits. |
| Bellows Imp | 1 | 35 | 4 (claw) | Fast, flanking. Appears in groups of 3-5. Ignites nearby hazards (dormant vents become active). |
| Crucible Sentinel | 3 | 200 | 16 (lance thrust), 8 (molten wave) | Elite guard. Lance thrust has 5-tile range. Molten wave is a 90-degree cone. Patrols forge stations. |
| Pyreth Automaton | 3 | 170 | 13 (multi-hit combo) | Ancient construct. 4-hit melee combo with 0.5s between strikes. Deactivates for 5s after combo (damage window). |

### Mini-Boss: Forge Overseer

| Property | Value |
|---|---|
| Tier | 3 |
| HP | 550 |
| Damage | 20 (anvil drop AoE), 12 (chain sweep) |
| Behavior | Commands nearby Constructs (+30% speed buff aura). Anvil drop targets player position with 1.5s delay. Chain sweep covers 270-degree arc. Destroying its power core (back weak point, 100 HP) disables the buff aura. |
| Spawn Chance | 40% per run |

## Boss: The Smelter

| Phase | HP Pool | Mechanics |
|---|---|---|
| Phase 1 (100-65%) | 750 | Arena is a circular crucible with a central molten pool. Smelter works the forge, periodically slamming the anvil (shockwave, 12 damage, dodgeable). Conveyor belts push players toward the molten center. 4 Pressure Locks on the arena edges -- activating all 4 stuns the Smelter for 5s. |
| Phase 2 (65-30%) | 525 | Molten pool expands, reducing safe ground by 40%. Smelter picks up a molten hammer -- melee combo attacks (15 damage per hit, 3-hit chain). Throws molten projectiles between combos. Pressure Locks reset and require reactivation. |
| Phase 3 (30-0%) | 250 | Smelter plunges into the molten pool, becoming a lava golem. Arena is now rings of safe ground around the central pool. Smelter erupts from random points. Players must bait eruptions near the cooling vents (3 total) to solidify and damage the Smelter. Each successful vent hit deals 80 damage + 3s stun. |

**Design Intent:** Combines environmental puzzle solving with combat. The Pressure Lock mechanic from the zone's puzzle rooms pays off as a boss mechanic. Players who invested in forge upgrades feel the payoff here.

## Hazard List

| Hazard | Effect | Duration | Telegraph |
|---|---|---|---|
| Pressure Lock | Timed door: closes in 8s, crushing anything in frame (instant kill) | Instant on close | Timer display, alarm klaxon, red-to-green light bar |
| Molten Floor | 6 DPS on contact, knockback toward center | Continuous | Orange-red glow, heat shimmer particles |
| Steam Vent | 10 damage burst + 2s blind (screen fog) | Instant burst | Hissing audio 1.5s before, vent cap rattles |
| Conveyor Belt | Forces movement in belt direction at 150% walk speed | Continuous | Visible belt animation, directional arrows |

## Art Direction

**Palette:** Hot orange (#FF6B35) for all heat sources, molten metal, and fire. Deep charcoal (#1A1A2E) for cooled surfaces, walls, and machinery. Bright white-yellow for the hottest elements (forge cores, the Smelter's heart). Cooling blue (#4A90D9) marks safe zones and rest areas.

**Architecture:** Industrial foundry merged with organic Mycelium growth. Massive bellows made of living tissue pump air into fungal furnaces. Conveyor systems of chitin and sinew move raw materials. The forge itself is a living organ -- the Mycelium's manufacturing center.

**VFX:** Heat distortion shader on all surfaces above temperature threshold. Molten metal uses fluid simulation with surface tension. Forge upgrades trigger a brief particle burst and weapon glow change. The Smelter's Phase 3 transformation uses a full-body dissolve-to-lava shader.

**Audio:** Hammering percussion at 140 BPM forms the rhythmic base. Brass stabs accent combat hits. Forge Station interaction uses a rising pitch anvil-strike sequence. The Smelter's Phase 3 replaces percussion with a low roar and intermittent eruption SFX.

## Level Design Guidelines

1. **Forge Placement:** First Forge Station must appear in rooms 3-5. Players need early access to understand the system before committing Echoes.
2. **Pressure Lock Training:** At least 2 Pressure Lock puzzles must appear before the boss. The boss fight's Pressure Lock mechanic must not be the first time players encounter it.
3. **Heat Management:** No room should have more than 50% of its floor as molten/hazardous surface. Players need space to maneuver.
4. **Conveyor Navigation:** Conveyor belts should always be visible and directionally clear. They are movement modifiers, not traps.
5. **Pyreth Murals:** Placed near Forge Stations. Each depicts a stage of forge-craft: raw material, the crucible, the shaping, and the finished form. The third mural hints at what the Pyreth were building -- components of the Necrospore Core.
6. **Cool-Down Rooms:** Rest rooms use a blue palette shift and reduced audio intensity. The contrast with the forge's heat is the zone's primary pacing tool.
