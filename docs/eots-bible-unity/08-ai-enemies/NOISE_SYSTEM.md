# Noise System

## Overview

Every player action generates a numeric noise value that propagates through the game world. Enemies use noise to detect and investigate player activity. The system is central to Zone 7 stealth gameplay but applies globally.

## Noise Values by Action

| Action | Noise Value | Notes |
|---|---|---|
| Standing still | 0 | No noise generation |
| Walking | 5 | Base movement |
| Crouching (walk) | 2 | Reduced noise option |
| Running | 15 | Standard traversal |
| Sprinting | 20 | Maximum speed movement |
| Jumping | 10 | Single burst on land |
| Dash/dodge | 20 | Single burst |
| Landing (high fall) | 25 | Height > 5 units |
| Light attack | 25 | Melee |
| Heavy attack | 40 | Melee |
| Ranged attack | 30 | Projectile launch |
| Ability activation | 30-60 | Varies by ability (defined per ability) |
| Spore-Talk (voice active) | 50 | Continuous while mic is active |
| Spore Plan execution | 80 | Large burst during execution phase |
| Item use (consumable) | 10 | Brief |
| Death/Overload | 100 | Maximum noise event |
| Fusion merge/separate | 40 | State transition burst |
| Environmental break | 35 | Destroying objects, walls |

## Noise Propagation

### Decay

- Noise decays at **-10 per second** (linear)
- Noise floor: 0 (cannot go negative)
- Decay is per-source (each player tracks their own noise level independently)

### Range

| Noise Level | Propagation Radius |
|---|---|
| 1-10 | 8 units |
| 11-25 | 15 units |
| 26-50 | 25 units |
| 51-80 | 40 units |
| 81-100 | 60 units (cross-room) |

- Noise propagates through walls at 50% intensity
- Noise propagates through closed doors at 25% intensity
- Open doorways transmit at 100%
- Noise does not propagate across zone boundaries

### Stacking

- Multiple noise sources within 1 second use the **maximum** value (not additive)
- Exception: different players' noise stacks additively at the listener position
- Example: two players running (15 each) in the same area produce 30 noise at the listener

## Alert Thresholds

| Enemy Type | Alert Threshold | Response |
|---|---|---|
| Tier 1 (Sporelings) | 30 | Enter ALERT state, face noise source |
| Tier 2 (Predators) | 30 | Investigate noise source |
| Tier 3 (Apex) | 20 | Investigate noise source |
| Mycelium Hunters | 20 | Investigate noise source (see MYCELIUM_HUNTERS.md) |
| Zone bosses | N/A | Bosses do not react to noise (always aware during fight) |

### Alert Escalation

| Stage | Trigger | Behavior |
|---|---|---|
| Unaware | Noise below threshold | Normal behavior (patrol/idle) |
| Alerted | Single noise above threshold | Turn toward source. 1s pause. Check LOS. |
| Investigating | Noise above threshold + no LOS | Move to noise source position. Search 8u radius for 10s. |
| Engaged | LOS confirmed during investigation | Enter combat (CHASE/ATTACK) |

## Noise Reduction

### Player Actions

| Modifier | Effect | Source |
|---|---|---|
| Crouch | -60% noise on movement | Default ability |
| Stealth boon (Tier 1) | -20% all noise | Boon system |
| Stealth boon (Tier 2) | -40% all noise | Boon system |
| Hush Silk (consumable) | 0 noise for 10s | Loot drop (rare) |
| Infection 80+ | +25% all noise | Infection penalty (involuntary) |
| Infection 95+ | +50% all noise | Severe infection penalty |

### Environmental Modifiers

| Environment | Modifier | Description |
|---|---|---|
| Water/acid puddle | +10 flat | Splashing |
| Metal floor | +5 flat | Resonant surface |
| Fungal carpet | -5 flat (min 0) | Sound-absorbing surface |
| Wind zone | -10 from listener perspective | Ambient noise masks player noise |

## Noise Visualization (Player HUD)

- Radial noise indicator in bottom-left corner of HUD
- Displays current personal noise level as a bar (0-100 scale)
- Color coding: green (0-19), yellow (20-39), orange (40-59), red (60+)
- Pulse effect when noise exceeds nearest enemy's alert threshold
- In Zone 7: additional directional indicator showing nearest Hunter's position relative to player

## Multiplayer Noise

| Scenario | Behavior |
|---|---|
| Solo player | Standard noise values |
| 2+ players in same room | Each player's noise evaluated independently per enemy |
| Fused entity | All movement noise +10 (larger entity, louder). Attack noise unchanged. |
| Different rooms | Noise propagates across rooms with wall/door attenuation |

## Debug Tools

| Tool | Description |
|---|---|
| Noise heatmap overlay | Shows noise levels as color gradient across the map (editor only) |
| Enemy hearing radius | Visualizes each enemy's detection radius in scene view |
| Noise log | Console output of all noise events with source, value, propagation |

## Implementation Notes

- `NoiseSystem` is an ECS singleton that processes all noise events each tick
- Noise events stored in `NativeList<NoiseEvent>` (source_id, position, value, timestamp)
- Propagation uses precomputed room adjacency graph (not raycasts per tick)
- Wall/door attenuation stored as edge weights in the adjacency graph
- Enemy hearing check: `NoiseSystem.GetNoiseAtPosition(enemy_pos)` returns max noise value
- Noise HUD element reads from `NoiseSystem.GetPlayerNoise(player_id)`
- Hush Silk sets a `NoiseSuppression` component with tick countdown
