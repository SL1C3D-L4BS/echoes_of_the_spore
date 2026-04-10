# Zone 02 -- The Cortex

## Overview

| Property | Value |
|---|---|
| Biome | Neural Fungal Network |
| Lock Ability | Synapse Link (granted by Spore Mother clear) |
| Color Temperature | Deep purple (#6C3483) + gold (#C9A84C) |
| Music Key | F# diminished, 75 BPM, pulsing synths + dissonant choir |
| Room Count Range | 20-30 |
| Atmosphere | Disorientation, alien beauty |
| Estimated Clear Time | 10-15 min |
| Fossil Mural Count | 3 (Luminari civilization) |

## Narrative Role

The Cortex is the Mycelium's thinking organ -- a vast neural web of pulsing filaments. The Luminari civilization understood cognition as communion; their murals depict minds dissolving willingly into the network. The zone asks: is the Mycelium sentient, or merely simulating sentience? The Architect boss embodies this ambiguity -- an intelligence that may be a person or an algorithm.

## Room Type Distribution

| Room Type | Min | Max | Notes |
|---|---|---|---|
| Combat | 7 | 12 | Thought Parasites appear as ambient hazards in 60% of combat rooms |
| Puzzle | 4 | 7 | Synapse Puzzles are the zone's signature; at least 3 per run |
| Reward | 2 | 4 | Boon offerings skew toward cognitive/perception boons |
| Rest | 2 | 3 | Neural alcoves with memory-playback ambient lore |
| Boss | 1 | 1 | Architect arena with teleportation grid |
| Transition | 3 | 4 | Long axon-corridor rooms with pulsing light sequences |
| Secret | 0 | 2 | 25% chance each; contain Luminari cipher fragments |

## Synapse Puzzle Mechanics

Synapse Puzzles are the Cortex's core non-combat verb. Players activate neural nodes in sequence to open pathways.

| Puzzle Tier | Node Count | Time Limit | Failure Penalty |
|---|---|---|---|
| Basic | 3-4 | 15s | Room resets, 1 Thought Parasite spawns |
| Standard | 5-6 | 12s | Room resets, 2 Thought Parasites + input reversal 3s |
| Advanced | 7-8 | 10s | Room resets, 3 Thought Parasites + vision inversion 5s |
| Master (secret rooms) | 9-10 | 8s | Full party wipe to room entrance, no penalty on retry |

**Rules:**
- Nodes glow in activation order for 2s before the sequence begins (memory phase).
- Wrong-node activation emits a dissonant tone and resets the chain.
- Co-op: in multiplayer, different nodes require different players to stand on them simultaneously (coordination check).
- Nodes are color-coded: gold (safe), purple (triggers hazard if activated out of order), red (decoy, never part of the solution).

## Enemy Roster

| Enemy | Tier | HP | Damage | Behavior |
|---|---|---|---|---|
| Synapse Spark | 1 | 35 | 4 (shock) | Floats along axon paths. Predictable patrol. Damage on contact. |
| Neural Leech | 1 | 45 | 6 (drain) | Attaches to player, drains 2 HP/s until shaken (mash or dodge). Steals 5 Echoes on attach. |
| Thought Parasite | 2 | 80 | 0 (debuff only) | No direct damage. Contact reverses left/right input for 3s. Invisible until within 4 tiles. |
| Cortex Sentinel | 2 | 120 | 10 (slam), 6 (pulse) | Guardian enemy. Stationary until player enters 5-tile radius. Ground slam + radial pulse combo. |

### Mini-Boss: Cognition Worm

| Property | Value |
|---|---|
| Tier | 2 |
| HP | 350 |
| Damage | 14 (bite), 8 (AoE thought pulse) |
| Behavior | Burrows through neural walls. Emerges with 0.8s telegraph. AoE thought pulse every 20s reverses all inputs for 2s. |
| Spawn Chance | 50% per run |

## Boss: The Architect

| Phase | HP Pool | Mechanics |
|---|---|---|
| Phase 1 (100-65%) | 600 | Teleports between 6 fixed grid points every 8s. Fires homing synapse bolts (3 damage, tracking for 2s). Spawns Synapse Sparks. |
| Phase 2 (65-30%) | 420 | Grid points reduce to 4. Spawns Thought Parasites. Arena tiles begin flashing in Synapse Puzzle sequences -- stepping on the correct sequence stuns the boss for 3s. |
| Phase 3 (30-0%) | 250 | Single teleport point. Constant input-reversal field (5-tile radius around boss). Must solve a 5-node Synapse Puzzle mid-combat to expose the core. |

**Design Intent:** Tests spatial awareness and puzzle-solving under pressure. Players who mastered Synapse Puzzles in earlier rooms have a significant advantage in Phase 2-3.

## Hazard List

| Hazard | Effect | Duration | Telegraph |
|---|---|---|---|
| Thought Parasite Field | Input reversal (left/right) | 3s | Purple shimmer on ground tiles, 1s warning |
| Axon Surge | 15 damage lightning along axon paths | Instant | Path glows bright gold for 1.5s before discharge |
| Memory Loop | Teleports player back 3 rooms | Instant | Swirling portal texture, avoidable with 2s reaction |
| Neural Static | Screen noise + audio distortion | 4s | Crackling audio cue 2s before onset |

## Art Direction

**Palette:** Deep purple (#6C3483) dominates walls and organic architecture. Gold (#C9A84C) marks all interactive and neural-pathway elements. Thought Parasites use a sickly green-purple gradient to signal danger.

**Architecture:** Rooms resemble cross-sections of neural tissue. Walls pulse with bioluminescent signals traveling along visible axon bundles. Floor tiles are semi-translucent, revealing deeper network layers beneath. Ceiling is absent -- the network extends infinitely upward (parallax illusion).

**Animation:** All environmental elements pulse at 75 BPM (synced to music). Node activation triggers a cascade of light along connected axons. Teleportation effects use a 0.3s dissolve-to-particles animation.

**Audio Layers:** Base pulsing synth pad (F#2). Puzzle rooms add a crystalline arpeggio that follows node activation order. Combat layers in dissonant choir. Boss arena introduces a heartbeat-like sub-bass that accelerates with phase transitions.

## Level Design Guidelines

1. **Axon Highways:** Long corridor rooms use visible axon paths as both navigation guides and hazard lanes. Players learn to read the network's pulse rhythm.
2. **Puzzle Gating:** At least one Synapse Puzzle per run must gate progression (not optional). This ensures all players engage with the mechanic before the boss.
3. **Vertical Disorientation:** Rooms should use inverted geometry (walkable ceilings, gravity-shifted platforms) in at least 20% of layouts to reinforce the zone's disorienting atmosphere.
4. **Luminari Murals:** Placed in rest rooms. Each mural depicts a stage of the Luminari's voluntary dissolution into the network -- arrival, communion, absorption.
5. **Thought Parasite Density:** Never more than 2 active Thought Parasites per room outside the boss arena. Input reversal is frustrating in excess.
6. **Teleport Anchors:** Every room with a Memory Loop hazard must have a visible anchor point players can memorize to avoid the teleport. Fairness over surprise.
