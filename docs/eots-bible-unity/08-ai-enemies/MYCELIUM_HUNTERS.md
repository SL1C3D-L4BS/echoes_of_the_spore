# Mycelium Hunters -- Zone 7 Exclusive

## Overview

Mycelium Hunters are the apex threat of the Necroveil. They cannot be killed through conventional means, deal instant death on contact, and transform Zone 7 into a stealth-horror experience. They are the only enemy that reacts to Spore-Talk voice chat.

## Core Properties

| Property | Value |
|---|---|
| HP | Effectively infinite (10,000 masks; no practical way to deplete) |
| Damage | Instant kill on contact (bypasses all shields, buffs, immunity) |
| Speed | Patrol: 2 u/s, Investigate: 4 u/s, Chase: 7 u/s (faster than player sprint at 6 u/s) |
| Detection | Sound-based (primary), Visual (secondary, 5-unit range cone) |
| Count per zone | 3-5 (fixed spawns, never despawn) |
| Zone | 7: Necroveil only |
| Ecosystem role | Apex of apex. All other enemies flee from Hunters. |

## Patrol System

### Route Types

| Type | Description | Count |
|---|---|---|
| Loop | Circular path through 3-5 rooms, repeating | 2 per zone instance |
| Pendulum | Back-and-forth between two points | 1 per zone instance |
| Sentinel | Stationary at key chokepoint, rotates in place | 1-2 per zone instance |

### Patrol Behavior

- Move at 2 u/s along predefined waypoints
- Pause at each waypoint for 3-5s (random)
- Patrol routes are fixed per zone seed (players can learn and memorize)
- Routes are visible on the map after discovering a Control Node (see Attunement below)
- Patrol routes intentionally overlap at key intersections (creating timing puzzles)

## Noise Detection

| Noise Source | Value | Hunter Response |
|---|---|---|
| Walking | 5 | No reaction |
| Running | 15 | No reaction |
| Jumping | 10 | No reaction |
| Dash | 20 | Investigate (threshold: 20) |
| Light attack | 25 | Investigate |
| Heavy attack | 40 | Investigate (aggressive) |
| Ability activation | 30-60 | Investigate |
| Spore-Talk (voice active) | 50 | Investigate (aggressive) |
| Spore Plan execution | 80 | Immediate chase to source |
| Death/Overload | 100 | Immediate chase to source |

Hunters investigate noise above 20 (vs 30 for normal Tier 2+).

### Investigation Behavior

1. Noise above threshold detected. Hunter marks noise source position.
2. Hunter moves to source at 4 u/s (investigate speed).
3. On arrival, searches 8-unit radius for 10s.
4. If player found (visual detection within 5 units): transition to CHASE.
5. If player not found: return to patrol route.

### Visual Detection

- Forward-facing cone: 5 units range, 60-degree arc
- If player enters cone: instant CHASE state
- No detection through walls (LOS required)
- Darkness has no effect on Hunter vision (they see in the dark)

## Chase Behavior

| Parameter | Value |
|---|---|
| Chase speed | 7 u/s (player sprint: 6 u/s) |
| Chase duration | Until contact OR target lost for 15 seconds |
| Target lost condition | No LOS and no noise > 20 for 15 consecutive seconds |
| Chase end behavior | Return to nearest patrol waypoint at investigate speed |
| Multi-target | Chases nearest player. Switches target if another player is closer AND making more noise. |

### Escape Strategies (Player)

| Strategy | Detail |
|---|---|
| Break LOS | Duck around corners. Hunter uses last-known-position pathfinding. |
| Go silent | Stop all inputs for 15s. Extremely difficult in combat. |
| Hide spots | Zone 7 has designated hide alcoves (visual: glowing mushroom clusters). Hunters do not check these. |
| Distraction | Throw noise-making items (if available) to redirect investigation. |
| Sacrifice | Another player draws attention with noise. |
| Attunement | At Infection 90+, use Control Node to command the Hunter (see below). |

## Attunement System

### Requirements

| Requirement | Value |
|---|---|
| Infection level | 90+ |
| Control Node | Must interact with a Control Node (1 per Hunter patrol route, hidden) |
| Interaction time | 5 seconds (channeled, interruptible) |

### Attunement Effects

| Effect | Duration | Detail |
|---|---|---|
| Hunter obedience | 60 seconds | Nearest Hunter follows attuned player |
| Attack redirect | 60 seconds | Hunter attacks enemies instead of players |
| Route reveal | Permanent (session) | Hunter's patrol route permanently visible on map |
| Infection cost | +5 Infection | Applied on attunement start |

### Limitations

- Only one player can attune to one Hunter at a time
- Attunement breaks if player's Infection drops below 85 (hysteresis)
- Attuned Hunter still instant-kills other players on contact (it obeys the attune, not the party)
- Control Node has 300s (5 min) cooldown after use
- If attuned player dies, Hunter enters 10s confusion state then resumes patrol

## Interaction with Other Systems

| System | Interaction |
|---|---|
| Spore-Talk | Active mic generates noise 50. Hunters will investigate voice chat. Players must use push-to-talk carefully. |
| Fusion | Fused entity is louder (all movement noise +10). Hunters chase fused entities more aggressively. |
| Spore Plan | Execution noise (80) attracts all Hunters in the zone. Extremely dangerous to use in Zone 7. |
| Ecosystem | All Tier 1 and Tier 2 enemies flee rooms containing a Hunter. Rooms on patrol routes have fewer enemies. |
| Boss (Veil Warden) | Hunters do not enter the boss room. Boss fight is conventional. |

## Visual and Audio Design

| Element | Description |
|---|---|
| Silhouette | Towering (3x player height), thin, elongated limbs, head is a cluster of fruiting bodies |
| Movement | Unnaturally smooth glide. No footstep sounds. |
| Idle sound | Low-frequency hum (30-50 Hz), felt more than heard. Increases as Hunter approaches. |
| Alert sound | Sharp chitinous click when investigating. Players hear this as warning. |
| Chase sound | Rhythmic pulsing drone. Volume and tempo increase with proximity. |
| Kill animation | Tendrils engulf player. Screen fills with mycelium. 2s animation. |

## Implementation Notes

- Hunters use a dedicated NavMesh agent layer (avoids Tier 1/2 obstacle avoidance)
- Patrol routes defined as `SplineContainer` components on route GameObjects
- Noise detection uses the shared `NoiseSystem` singleton (see NOISE_SYSTEM.md)
- Kill is implemented as `InstantKillOnContact` component checking trigger collider overlap each physics tick
- Attunement state stored as `NetworkVariable<AttunementData>` on the Hunter's NetworkObject
- Hide spots use trigger volumes that set `PlayerHidden=true`, suppressing visual detection
- Hunter model uses custom shader: subsurface scattering + pulsing bioluminescence
