# Tier 3: Apex (Mini-Bosses)

## Overview

Apex enemies are rare, powerful mini-bosses that define rooms. They hunt Tier 2 Predators, never flee, and feature phase transitions triggered by HP thresholds. Each Apex has unique mechanics that force players to adapt mid-fight.

## Shared Tier 3 Properties

| Property | Value |
|---|---|
| HP range | 25-50 masks |
| Damage | 2-3 masks per hit |
| AI type | Behavior Tree with Phase Transitions |
| Count per room | 0-1 |
| Spawn chance | 5% per hour of room being unvisited |
| Max per zone instance | 3 (hard cap across all rooms) |
| Drops | 15-25 Silk, rare materials, guaranteed ability shard |
| Aggro range | Entire room (always aware of player presence) |
| Leash range | Room boundaries (never leaves their room) |
| Flee behavior | Never flees |

## Phase Transition Template

| Phase | HP Threshold | General Pattern |
|---|---|---|
| Phase 1 | 100% - 60% | Core attack set (2-3 attacks). Teaches patterns. |
| Phase 2 | 60% - 25% | New attacks added. One core attack enhanced. Arena effect. |
| Phase 3 | 25% - 0% | All attacks faster. Unique desperation mechanic. |

Transitions: 1.5s invulnerability during phase change. Visual roar/transformation animation. Shockwave pushes players back 5 units.

## Apex Types

### Hive Matriarch

| Property | Value |
|---|---|
| HP | 40 masks |
| Speed | 2 u/s (slow, massive) |
| Spawn zones | Zones 2-4 |
| Arena | Requires 15x15 unit minimum room |

**Phase 1 (100%-60%)**

| Attack | Damage | Speed | Description |
|---|---|---|---|
| Swarm Spawn | 0 | Every 8s | Spawns 3 Spore Mites from egg sacs |
| Ground Slam | 3 masks | 1.5s windup | 4 unit AoE, knockback |
| Tendril Sweep | 2 masks | 0.8s | 180-degree frontal cone, 5 units |

**Phase 2 (60%-25%)**
- Spawned Mites upgrade to Tendril Creepers
- Ground Slam creates 3 spore cloud patches (2s duration, 1 Infection/s)
- New attack: Egg Barrage (lobs 5 eggs across room, each hatches in 3s)

**Phase 3 (25%-0%)**
- Continuous spawning (every 4s)
- Ground Slam radius doubled (8 units)
- Desperation: Matriarch burrows underground, emerges at player location (3 masks + stagger)

### The Distillery

| Property | Value |
|---|---|
| HP | 35 masks |
| Speed | 3 u/s |
| Spawn zones | Zones 3-5 |
| Arena | Requires acid-resistant platforms in room |

**Phase 1 (100%-60%)**

| Attack | Damage | Speed | Description |
|---|---|---|---|
| Acid Spray | 2 masks + 2 Infection | 1s | 60-degree cone, 6 unit range |
| Body Check | 2 masks | 0.6s | Charge 4 units forward |
| Regeneration | Self-heal 2 masks | Passive | Heals every 10s if not hit |

**Phase 2 (60%-25%)**
- Acid Spray leaves persistent puddles (8s duration)
- Regeneration increases to 3 masks / 8s
- New attack: Acid Rain (room-wide, 1 mask/s for 3s, safe zones marked 1s before)

**Phase 3 (25%-0%)**
- Regeneration: 4 masks / 6s (must sustain DPS to kill)
- Acid puddles permanent until Distillery dies
- Desperation: splits into 2 half-HP copies (17 masks each, must kill both)

### Vein Warden

| Property | Value |
|---|---|
| HP | 50 masks |
| Speed | 0 (stationary Phase 1-2), 4 u/s (Phase 3) |
| Spawn zones | Zones 4-6 |
| Special | Blocks passage. Must be killed to proceed. |

**Phase 1 (100%-60%)**

| Attack | Damage | Speed | Description |
|---|---|---|---|
| Vein Lash | 2 masks | 0.5s | 8 unit range tendril whip |
| Wall Spikes | 2 masks | 1s telegraph | Spikes erupt from walls in player's direction |
| Root Grab | 0 + 3s root | 1.2s | Roots emerge at player feet, 3 unit radius |

**Phase 2 (60%-25%)**
- Vein Lash chains to 2 targets (2P+)
- Wall Spikes fire from ceiling as well
- New attack: Vein Network (floor veins glow, explode in sequence like a fuse, 2 masks each)

**Phase 3 (25%-0%)**
- Warden uproots and becomes mobile (4 u/s)
- All attacks available while moving
- Desperation: room slowly constricts (walls close in over 60s, reducing arena by 50%)

### Canker Bloom

| Property | Value |
|---|---|
| HP | 30 masks |
| Speed | 5 u/s (fast) |
| Spawn zones | Zones 5-7 |
| Special | Infection aura: +1 Infection/3s to all players within 10 units |

**Phase 1 (100%-60%)**

| Attack | Damage | Speed | Description |
|---|---|---|---|
| Petal Barrage | 1 mask x5 | 0.3s per petal | Scatter projectiles, wide spread |
| Pollen Cloud | 0 + blind (3s) | 1s | 5 unit radius, obscures vision |
| Bloom Dash | 2 masks | 0.4s | Dashes through player, leaves pollen trail |

**Phase 2 (60%-25%)**
- Infection aura range increases to 15 units
- Petal Barrage tracks player (homing, 0.5 turn rate)
- New attack: Symbiosis (attaches to a Tier 2 enemy if present, heals it 2 masks/s, must kill carrier first)

**Phase 3 (25%-0%)**
- Infection aura: +2 Infection/2s
- All attacks inflict +3 Infection on hit
- Desperation: Full Bloom (room fills with pollen over 30s, constant 1 mask/s damage to all players unless they find the 1 safe pocket that shifts every 5s)

## Ecosystem Interaction

| Behavior | Detail |
|---|---|
| Hunts Tier 2 | Apex actively kills Predators in their room |
| Territorial | Only 1 Apex per room. If simulation would spawn 2, lower-HP one relocates. |
| Clears rooms | A room with an Apex has 0-2 Tier 2 enemies (hunted) but normal Tier 1 (Apex ignores fodder) |
| Player priority | Always targets players over Tier 2 if players are in the room |

## Loot Table

| Drop | Chance | Quantity |
|---|---|---|
| Silk | 100% | 15-25 |
| Infection | 100% | +3 base |
| Rare material | 60% | 1-2 |
| Ability shard | 100% | 1 |
| Apex trophy (cosmetic) | 20% | 1 |

## Implementation Notes

- Phase transitions managed by `ApexPhaseController` component (listens to HP change events)
- Invulnerability during transition uses `DamageImmunity` flag on `HealthComponent`
- Shockwave on phase change uses `OverlapSphere` + `AddForce` on all entities in range
- Arena effects (constricting walls, acid puddles) are server-authoritative `HazardZone` entities
- Apex spawn check runs as a background job every 60s per room via `EcosystemTickSystem`
