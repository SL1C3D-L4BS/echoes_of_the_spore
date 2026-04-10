# Zone 01 -- Sporehaven

## Overview

| Property | Value |
|---|---|
| Biome | Bioluminescent Marsh |
| Lock Ability | None (starting zone) |
| Color Temperature | Warm green (#2ECC71) + cool cyan (#00FFD1) |
| Music Key | D minor, 90 BPM, ambient pads + plucked strings |
| Room Count Range | 15-25 |
| Atmosphere | Wonder, safety, introduction |
| Estimated Clear Time | 8-12 min (first run), 4-6 min (experienced) |
| Fossil Mural Count | 3 (Progenitor civilization) |

## Narrative Role

Sporehaven is the player's first contact with the living Mycelium. Every element teaches without lecturing: Fusion Nodes glow invitingly, enemies telegraph attacks for longer than in later zones, and the Progenitor murals seed the central mystery -- who built the network, and why did they vanish? The zone must feel beautiful enough that players want to return, and legible enough that death always feels fair.

## Room Type Distribution

| Room Type | Min | Max | Notes |
|---|---|---|---|
| Combat | 6 | 10 | Tier 1 only; first room is always a gated tutorial fight |
| Puzzle | 2 | 4 | First Fusion Node puzzle appears in room 2-3 |
| Reward | 2 | 3 | Guaranteed Boon offering after first puzzle clear |
| Rest | 2 | 3 | Healing pools; one always precedes the boss corridor |
| Boss | 1 | 1 | Final room, locked until all combat rooms cleared |
| Transition | 2 | 3 | Connecting corridors with ambient storytelling |
| Secret | 0 | 1 | 30% spawn chance; contains Progenitor lore fragment |

## Enemy Roster

| Enemy | Tier | HP | Damage | Behavior |
|---|---|---|---|---|
| Puffcap | 1 | 40 | 5 (contact), 8 (burst) | Wanders; inflates for 1.5s then bursts in 3-tile AoE. Telegraphed by color shift from cyan to orange. |
| Tendril Creeper | 1 | 55 | 6 (lash) | Anchored to walls; extends tendrils up to 4 tiles. Retract window: 0.8s. |
| Spore Mite | 1 | 20 | 3 (contact) | Swarm unit, spawns in groups of 4-6. Individually weak; dangerous in clusters. Flees below 50% HP. |
| Marsh Lurker | 1 | 65 | 7 (grab) | Submerged ambush predator. 1.2s telegraph (bubbles). Grab holds player 1s unless mash-free. |

### Mini-Boss: Elder Puffcap

| Property | Value |
|---|---|
| Tier | 2 |
| HP | 280 |
| Damage | 12 (burst), 8 (spore cloud) |
| Behavior | Three-phase inflate cycle. Spawns 3 Spore Mites per burst. Weak spot on dorsal cap (2x damage). |
| Spawn Chance | 40% per run; replaces one combat room |

## Boss: The Spore Mother

| Phase | HP Pool | Mechanics |
|---|---|---|
| Phase 1 (100-60%) | 500 | Stationary. Spawns Puffcaps from 4 root nodes. Radial spore burst every 12s (dodge window: 1.5s). |
| Phase 2 (60-30%) | 300 | Root nodes retract. Tendril sweep attacks (3-tile arc). Spawns Tendril Creepers on walls. |
| Phase 3 (30-0%) | 200 | Arena floods with spore cloud (vision -60%). Mother reveals core -- high damage window but surrounded by Mites. |

**Design Intent:** Teaches pattern recognition, add management, and the value of Fusion abilities. Players who fuse before this fight have a meaningful advantage.

## Hazard List

| Hazard | Effect | Duration | Telegraph |
|---|---|---|---|
| Spore Cloud | Vision radius -40% | 5s | Green particle drift, 1.5s warning |
| Marsh Gas Vent | 10 damage + knockback 2 tiles | Instant | Bubbling ground, 2s warning |
| Bioluminescent Flare | Blinds (screen whiteout) | 1.5s | Pulsing glow intensifies over 3s |
| Sticky Moss | Movement speed -50% | 3s (while in contact) | Visible texture difference on tiles |

## Art Direction

**Palette:** Dominant warm cyan (#00FFD1) with deep marsh green (#0D4D2B) backgrounds. Accent warm amber (#FFB347) for interactive elements. Boss arena shifts to warning orange during Phase 3.

**Lighting:** Bioluminescent sources only -- no artificial light. Fungi on walls pulse at 0.5 Hz. Water reflects ceiling glow with 0.3s delay. God rays through canopy gaps in transition rooms.

**Vegetation:** Oversized mushroom caps (3-8 tile diameter) serve as platforms. Moss carpets with subtle parallax. Hanging spore chains act as environmental storytelling (density increases near boss).

**Audio Layers:** Base ambient pad (D2 drone) always present. Combat adds plucked strings and low toms. Puzzle rooms introduce a melodic motif on kalimba. Boss arena layers in a choir on Phase 2.

## Level Design Guidelines

1. **First Room Rule:** Room 1 is always a safe space with a single Puffcap and a Fusion Node tutorial prompt. No hazards, no timers.
2. **Sightline Principle:** Players should always see at least one exit and one point of interest from any position. No dead-end corridors longer than 6 tiles.
3. **Vertical Layering:** At least 30% of combat rooms must use mushroom-cap platforms to teach vertical play early.
4. **Mural Placement:** Fossil Murals appear in transition rooms only, never in combat rooms. Players should feel safe while absorbing lore.
5. **Pacing Curve:** Combat intensity follows a 2-1 rhythm -- two combat rooms, then one non-combat room. Boss corridor always follows a rest room.
6. **Secret Room Tell:** Secret rooms are hinted by asymmetric wall glow patterns. Breakable walls emit a faint audio cue (0.2 Hz pulse) within 3 tiles.
7. **Water Depth:** Marsh water is cosmetic in the first 5 rooms. From room 6 onward, deep water zones (darker blue tint) slow movement by 30%, teaching hazard awareness before Fermentation Pits.
