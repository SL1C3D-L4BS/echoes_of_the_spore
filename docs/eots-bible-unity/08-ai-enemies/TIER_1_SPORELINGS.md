# Tier 1: Sporelings (Fodder Enemies)

## Overview

Tier 1 enemies are the most common encounters. They use simple finite state machines, exist in large groups, and serve as combo-building fodder, resource sources, and food for the ecosystem's predation chain.

## Shared Tier 1 Properties

| Property | Value |
|---|---|
| HP range | 2-5 masks |
| Damage | 1 mask per hit |
| AI type | Finite State Machine |
| Count per room | 5-15 |
| Respawn rule | 50% of original count on room re-entry |
| Loot | Common Silk (100%), Infection +1 per kill (80%) |
| Ecosystem role | Prey for Tier 2 |

## FSM States (All Tier 1)

```
IDLE → ALERT → CHASE → ATTACK → FLEE → IDLE
```

| State | Entry Condition | Behavior | Exit Condition |
|---|---|---|---|
| IDLE | Default / no threat | Wander within 5-unit radius of spawn point | Player within detection range OR noise > 30 |
| ALERT | Threat detected | Face threat source, 1s pause | Player within chase range → CHASE; threat lost → IDLE |
| CHASE | Player in chase range | Move toward player at move speed | Player within attack range → ATTACK; player out of chase range for 5s → IDLE |
| ATTACK | Player in attack range | Execute attack pattern | Attack complete → CHASE (re-evaluate); HP < 20% → FLEE |
| FLEE | HP below 20% | Move away from player at 1.5x move speed | 10 units from player → IDLE; cornered (no path) → ATTACK |

## Enemy Types

### 1. Puffcap

| Property | Value |
|---|---|
| HP | 2 masks |
| Speed | 0 (stationary) |
| Detection range | 4 units (proximity only, no LOS check) |
| Attack | Self-destruct: 1 mask AoE (3-unit radius) |
| Attack telegraph | 1.5s swell + color change (yellow to red) |
| Behavior | Stationary. Detonates when player enters range. 0.5s arm time prevents instant damage. |
| Spawn preference | Narrow corridors, doorways, ledge edges |
| Special | Chain reaction: explosion triggers adjacent Puffcaps within 3 units |
| Variants | Toxic Puffcap (Zone 3): leaves poison cloud for 5s, +2 Infection on contact |

### 2. Tendril Creeper

| Property | Value |
|---|---|
| HP | 3 masks |
| Speed | Walk: 2 u/s, Lunge: 8 u/s |
| Detection range | 8 units (LOS required) |
| Chase range | 12 units |
| Attack range | 4 units |
| Attack | Lunge: 1 mask, 0.3s startup, covers 4 units |
| Attack telegraph | Rears back, tendrils glow green |
| Behavior | Wall-crawling. Patrols on walls and ceilings. Drops to floor to lunge at player. |
| Spawn preference | Rooms with vertical geometry, pillars, overhangs |
| Special | Wall cling: immune to ground-level AoE while on walls |
| Variants | Barbed Creeper (Zone 4): lunge inflicts bleed (1 mask over 3s) |

### 3. Spore Mite

| Property | Value |
|---|---|
| HP | 2 masks (per individual) |
| Speed | Walk: 3 u/s |
| Detection range | 6 units (shared: if one detects, swarm detects) |
| Chase range | 10 units |
| Attack range | 1.5 units |
| Attack | Bite: 1 mask, 0.2s startup |
| Attack telegraph | Mandibles click (audio cue), brief pause |
| Behavior | Swarm AI. Moves in groups of 5-8. Shared aggro. Surrounds player. |
| Spawn preference | Open rooms, fungal gardens |
| Special | Swarm bonus: +10% speed per additional Mite within 3 units of target. Max +50%. |
| Variants | Glowing Mite (Zone 5): emits light, reveals hidden paths when swarm is alive |

### 4. Rot Drifter

| Property | Value |
|---|---|
| HP | 4 masks |
| Speed | Float: 1.5 u/s (slow, erratic movement) |
| Detection range | 10 units (detects noise only, blind) |
| Chase range | 15 units |
| Attack range | 2 units |
| Attack | Spore burst: 1 mask + Infection +2, 0.8s startup |
| Attack telegraph | Body expands, spore particles visible |
| Behavior | Drifts randomly. Noise-reactive. Does not respond to visual stimuli. |
| Spawn preference | Dark rooms, caverns |
| Special | Blind: immune to visual aggro. Only responds to noise > 15. Walking silently bypasses entirely. |
| Variants | Hollow Drifter (Zone 7): explodes on death dealing 2 mask AoE |

### 5. Myco-Sprout

| Property | Value |
|---|---|
| HP | 5 masks |
| Speed | 0 (rooted) → 2 u/s (uprooted after first hit) |
| Detection range | 6 units |
| Attack range | 5 units (ranged) |
| Attack | Spore spit: 1 mask projectile, 3/s fire rate, arcing trajectory |
| Attack telegraph | Stem tilts toward target, 0.5s aim |
| Behavior | Rooted turret. Takes one hit to uproot, then chases while firing. |
| Spawn preference | Elevated platforms, room edges |
| Special | Rooted state: 50% damage reduction. Uprooted: no damage reduction, can be knocked back. |
| Variants | Acid Sprout (Zone 3): projectiles leave acid puddle on impact (2s duration) |

## Spawn Rules

| Rule | Value |
|---|---|
| Initial population | 5-15 per room (zone-dependent density multiplier) |
| Composition | Random from zone's available Tier 1 pool (each zone has 2-3 types) |
| Placement | Pre-placed spawn points in room templates + procedural scatter |
| Respawn | On room re-entry: 50% of original count at original spawn points |
| Ecosystem decay | If room unvisited 10+ min, Tier 2 may have killed some/all Tier 1 |

## Zone Availability

| Zone | Available Tier 1 Types |
|---|---|
| 1: Sporehaven | Puffcap, Spore Mite, Myco-Sprout |
| 2: Cortex | Tendril Creeper, Spore Mite |
| 3: Ferment Pits | Toxic Puffcap, Acid Sprout, Rot Drifter |
| 4: Chitinhall | Barbed Creeper, Spore Mite |
| 5: Rot Garden | Glowing Mite, Myco-Sprout, Rot Drifter |
| 6: Myco-Forge | Puffcap, Tendril Creeper |
| 7: Necroveil | Hollow Drifter, Spore Mite |
| 8: Necrospore Core | All types (AI Director selects) |

## Implementation Notes

- FSM implemented as `SporelingFSM` MonoBehaviour with `enum SporelingState`
- Swarm behavior (Spore Mite) uses flocking algorithm: alignment + cohesion + separation + target seek
- Wall-crawling (Tendril Creeper) uses NavMesh surface links between floor/wall/ceiling NavMeshes
- Chain reaction (Puffcap) implemented via Physics.OverlapSphere on detonation
- All Tier 1 pooled via ObjectPool<T> for performance (no runtime instantiation)
