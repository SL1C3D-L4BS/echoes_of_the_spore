# Zone 03 -- Fermentation Pits

## Overview

| Property | Value |
|---|---|
| Biome | Acidic Brewery Caves |
| Lock Ability | Acid Membrane (Architect clear OR Community Tree Depth 3) |
| Color Temperature | Sickly yellow-green (#A3B818) + corrosion orange (#D4731A) |
| Music Key | Bb minor, 110 BPM, industrial percussion + distorted bass |
| Room Count Range | 20-35 |
| Atmosphere | Disgust, industrial grime, dark humor |
| Estimated Clear Time | 12-18 min |
| Fossil Mural Count | 4 (Ferrox civilization) |

## Narrative Role

The Fermentation Pits are the Mycelium's digestive system -- a place where organic matter is broken down and recycled. The Ferrox civilization worshipped decay as transformation, building vast brewery-temples to accelerate decomposition. Their murals frame acid not as destruction but as liberation from fixed form. The Distiller boss is a Ferrox creation left running long after its makers dissolved.

## Acid Mechanic Specification

pH is the zone's core system. Each room has a pH value (1.0-14.0) displayed on the HUD as a color-coded bar.

### pH Damage Tiers

| pH Range | Classification | Effect on Player | Effect on Enemies |
|---|---|---|---|
| 6.0-8.0 | Neutral | No effect | Normal behavior |
| 4.0-5.9 | Mildly Acidic | 1 DPS, yellow screen tint | -10% speed, +10% damage taken |
| 2.0-3.9 | Acidic | 3 DPS, orange screen tint, metal SFX | -25% speed, +25% damage taken, some flee |
| 0.0-1.9 | Corrosive | 6 DPS, red screen tint, armor degrades 1%/s | -50% speed, Tier 1 enemies dissolve instantly |
| 8.1-10.0 | Mildly Alkaline | Heal 1 HP/s | +10% speed, -10% damage taken |
| 10.1-14.0 | Alkaline | Heal 2 HP/s but vision blur | +25% speed, +25% HP regen, become aggressive |

### pH Interaction Rules

- **Player Abilities:** Certain abilities shift room pH. Acid-type attacks lower pH by 0.5 per use. Healing abilities raise pH by 0.3.
- **Environmental Sources:** Acid vents lower pH by 1.0 every 15s. Alkaline pools raise pH by 0.5 on contact.
- **pH Storms:** Scripted events that drop pH to 1.5 across the entire room for 8s. Telegraphed 2s before by orange rain particles and a klaxon SFX.
- **Strategic Play:** Skilled players can manipulate pH to dissolve weak enemies while managing their own damage intake.

### Visual Warning System

| pH Level | Ground Color | Particle Effect | Audio Cue |
|---|---|---|---|
| Neutral | Grey stone | None | Ambient drip |
| Mild Acid | Yellow-green sheen | Light fizz particles | Soft hiss |
| Acidic | Orange glow | Dense bubble particles | Moderate sizzle |
| Corrosive | Bright red, glowing | Heavy steam + dissolve particles | Loud chemical roar |
| Alkaline | Cool blue tint | Gentle mist | Harmonic chime |

## Room Type Distribution

| Room Type | Min | Max | Notes |
|---|---|---|---|
| Combat | 8 | 14 | pH manipulation is viable in all combat rooms |
| Puzzle | 3 | 5 | pH-balancing puzzles: route acid flows to dissolve barriers |
| Reward | 2 | 4 | Acid-resistant boons weighted 2x in loot tables |
| Rest | 2 | 3 | Alkaline pools (safe zones, pH 9.0+) |
| Boss | 1 | 1 | Distiller arena with flooding mechanic |
| Transition | 3 | 5 | Pipe network corridors with dripping hazards |
| Secret | 0 | 2 | Hidden behind acid-dissolvable walls (requires pH < 2.0) |

## Enemy Roster

| Enemy | Tier | HP | Damage | Behavior |
|---|---|---|---|---|
| Acid Spitter | 1 | 45 | 7 (ranged acid glob) | Stationary turret. Fires every 3s. Glob leaves pH -0.3 puddle for 5s. |
| Slime Crawler | 1 | 50 | 5 (contact) | Moves through acid unharmed. Splits into 2 at 50% HP (each half: 25 HP). |
| Corroded Husk | 2 | 100 | 9 (slam), 4 (acid aura) | Melee brute. Passive acid aura lowers room pH by 0.1/s while alive. |
| Brew Tender | 2 | 85 | 6 (flask throw) | Throws pH-altering flasks. Red flasks lower pH by 1.0. Blue flasks raise pH by 1.0. Prioritizes maintaining dangerous pH. |
| Ferrox Remnant | 3 | 160 | 12 (charge) | Rare. Ancient construct. Immune to all pH effects. Charges in straight lines, 1s wind-up. |

### Mini-Boss: Vat Keeper

| Property | Value |
|---|---|
| Tier | 2 |
| HP | 400 |
| Damage | 15 (acid wave), 10 (slam) |
| Behavior | Guards a central vat. Periodically tips it, flooding half the arena (pH 1.0 for 6s). Players must use elevated platforms. Weak point exposed during tip animation. |
| Spawn Chance | 45% per run |

## Boss: The Distiller

| Phase | HP Pool | Mechanics |
|---|---|---|
| Phase 1 (100-60%) | 700 | Arena has 4 acid vats at cardinal positions. Distiller rotates between them, drinking to power up. Destroy vats to limit its attacks. Room pH: 5.0 baseline. |
| Phase 2 (60-25%) | 500 | Arena floor floods to ankle depth (pH 3.0). Distiller becomes mobile, charges between remaining vats. Acid geysers erupt from destroyed vat locations. |
| Phase 3 (25-0%) | 250 | Full arena flood (pH 1.5). Elevated platforms are the only safe ground. Distiller is stationary at center, firing omnidirectional acid streams. DPS race. |

**Design Intent:** Tests resource management and spatial awareness. Players who learned pH manipulation have tools to mitigate Phase 3's extreme environment.

## Hazard List

| Hazard | Effect | Duration | Telegraph |
|---|---|---|---|
| pH Storm | Room pH drops to 1.5, 2 DPS everywhere | 8s | Orange rain + klaxon, 2s warning |
| Acid Geyser | 15 damage + knockback, pH -1.0 in 3-tile radius | Instant | Ground cracks glow orange, 1.5s warning |
| Pipe Burst | Acid stream across room, 4 DPS on contact | 6s | Pipe rattles and leaks, 2s warning |
| Fermentation Bubble | Traps player in bubble, floats upward for 3s | 3s | Visible bubble rising from floor pools |

## Art Direction

**Palette:** Sickly yellow-green (#A3B818) for acid surfaces. Corrosion orange (#D4731A) for metal and pipes. Dark brown (#3E2723) for stone. Bright red warnings for corrosive-pH zones.

**Architecture:** Industrial brewery aesthetics merged with organic cave systems. Copper-green pipes run along walls, leaking at joints. Vats of bubbling liquid serve as environmental hazards and light sources. Floor textures shift color dynamically based on current room pH.

**VFX:** Acid surfaces use a custom shader with animated caustics. pH changes trigger a full-room color grade shift over 0.5s. Dissolving enemies use a vertex-displacement dissolve effect from feet upward.

**Audio:** Base industrial percussion loop. Acid surfaces emit a constant low hiss scaled to pH danger level. Flask impacts use distinct glass-break + sizzle SFX. Boss arena layers in distorted bass guitar during Phase 2-3.

## Level Design Guidelines

1. **pH Literacy:** The first 3 rooms must teach pH mechanics safely -- mild acid only, clearly marked safe zones, and a Brew Tender enemy that demonstrates flask interactions.
2. **Elevation Matters:** At least 40% of rooms must have elevated safe platforms. Players need to internalize "go up when acid rises" before the boss.
3. **Secret Access:** Acid-wall secrets require players to intentionally lower pH below 2.0 -- a deliberate risk/reward tradeoff. Secret walls have visible corrosion marks as tells.
4. **Ferrox Murals:** Placed in transition rooms near vats. Each depicts a stage of the Ferrox philosophy: matter entering the vat, dissolving, and re-emerging transformed.
5. **Pacing:** No more than 2 consecutive pH Storm events per run. The system should feel strategic, not punishing.
6. **Co-op pH:** In multiplayer, different players can manipulate pH simultaneously, enabling coordinated strategies (one player tanks acid while another heals the room).
