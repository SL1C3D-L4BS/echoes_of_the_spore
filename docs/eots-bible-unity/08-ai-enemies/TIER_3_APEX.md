# Tier 3: Apex (Mini-Bosses)

## Overview

Tier 3 enemies are rare, powerful encounters that define the rooms they occupy. They use behavior trees with phase transitions triggered by HP thresholds. Each Apex has unique mechanics that demand specific strategies.

## Shared Tier 3 Properties

| Property | Value |
|---|---|
| HP range | 25-50 masks |
| Damage | 2-3 masks per hit |
| AI type | Behavior Tree with Phase Transitions |
| Count per room | 0-1 |
| Spawn chance | 5% per hour the room is unvisited |
| Respawn rule | Does not respawn on room re-entry. New roll on next visit cycle. |
| Loot | Rare Silk (100%), Unique material (30%), Infection +5 per kill |
| Ecosystem role | Apex predator. Hunts Tier 2. |

## Phase Transition System

All Apex enemies share a two-phase transition model:

| Phase | HP Threshold | Behavior Change |
|---|---|---|
| Phase 1 | 100% - 50% | Standard attack patterns. Learning phase for player. |
| Phase 2 | 50% - 0% | Enhanced attacks. New abilities unlocked. Aggression increases. |

Transition animation: 2s invulnerability + visual transformation + AoE shockwave (1 mask, 5-unit radius).

## Apex Types

### 1. Hive Matriarch

| Property | Value |
|---|---|
| HP | 40 masks |
| Speed | Walk: 1.5 u/s (slow, lumbering) |
| Detection range | 15 units |
| Spawn zones | 1, 4 |

**Phase 1 Attacks:**

| Attack | Damage | Telegraph | Cooldown |
|---|---|---|---|
| Ground slam | 3 masks (AoE 4u radius) | Raises both arms, 1s wind-up | 6s |
| Summon swarm | Spawns 4 Spore Mites | Abdomen pulses green, 2s | 12s |
| Acid spit | 2 masks (ranged, arcing) | Head tilts back, 0.8s | 4s |

**Phase 2 Changes:**
- Summon swarm now spawns 6 Spore Mites + 1 Tendril Creeper
- Ground slam leaves acid pool (5s duration, 1 mask/s contact damage)
- New attack: Brood Burst (sacrifice 3 existing summons to create 3-unit AoE explosion per summon, 2 masks each)
- Movement speed increases to 2.5 u/s

**Unique Mechanic:** Summoned creatures tether to Matriarch. If all summons die, Matriarch is staggered for 3s.

### 2. The Distillery

| Property | Value |
|---|---|
| HP | 35 masks |
| Speed | Walk: 2 u/s |
| Detection range | 12 units |
| Spawn zones | 3, 5 |

**Phase 1 Attacks:**

| Attack | Damage | Telegraph | Cooldown |
|---|---|---|---|
| Acid spray (cone) | 2 masks (5u range, 60-degree arc) | Mouth opens wide, green mist, 0.6s | 5s |
| Regeneration pulse | Heals 3 masks | Glows green, 1.5s channel (interruptible) | 10s |
| Body slam | 2 masks + knockback 4u | Rears up, 0.8s | 7s |

**Phase 2 Changes:**
- Regeneration pulse heals 5 masks and is no longer interruptible
- Acid spray becomes 120-degree arc
- New attack: Acid Rain (room-wide, 1 mask/s for 4s, safe zones marked by dry patches)
- Passive: acid trail left while moving (1 mask on contact, 3s duration)

**Unique Mechanic:** Regeneration can be countered by dealing 5+ masks during the channel window, causing a "purge" that deals 3 masks back to the Distillery.

### 3. Vein Warden

| Property | Value |
|---|---|
| HP | 50 masks |
| Speed | 0 (stationary Phase 1) → 3 u/s (mobile Phase 2) |
| Detection range | Entire room (omniscient within its room) |
| Spawn zones | 6, 7 |

**Phase 1 Attacks:**

| Attack | Damage | Telegraph | Cooldown |
|---|---|---|---|
| Vine lash | 2 masks (ranged, 10u reach) | Vine rears back, 0.5s | 3s |
| Root grab | 0 damage, immobilizes 2s | Ground cracks beneath player, 0.8s | 8s |
| Spore barrage | 1 mask x5 (scattered projectiles) | Pods on body swell, 1s | 6s |

**Phase 2 Changes:**
- Uproots: now mobile at 3 u/s. Room shakes during transition.
- Vine lash becomes sweeping (180-degree arc, 2 masks)
- New attack: Tunnel (burrows underground for 3s, emerges beneath player for 3 masks)
- Root grab now affects all players within 6 units simultaneously

**Unique Mechanic:** While rooted, destroying 3 exposed root nodes (each has 5 masks HP, positioned around the room) deals 10 masks to the Warden and forces Phase 2 early.

### 4. Shriek Hollow

| Property | Value |
|---|---|
| HP | 30 masks |
| Speed | Walk: 3 u/s, Dash: 8 u/s |
| Detection range | Sound-based only (no visual detection). Detects noise > 10. |
| Spawn zones | 2, 7 |

**Phase 1 Attacks:**

| Attack | Damage | Telegraph | Cooldown |
|---|---|---|---|
| Sonic scream | 2 masks (cone 8u, 90-degree) | Inhales deeply, 1s | 5s |
| Lunging bite | 3 masks (melee dash) | Crouches, 0.5s | 4s |
| Echo pulse | 0 damage, reveals all players for 10s | Body vibrates, 0.3s | 15s |

**Phase 2 Changes:**
- Sonic scream becomes 180-degree arc
- Echo pulse now also slows players by 30% for 5s
- New attack: Silence (disables all player abilities for 3s, room-wide, 20s cooldown)
- Passive: constant low noise emission attracts other enemies to the room

**Unique Mechanic:** Completely blind. If all players remain perfectly still (no input) for 3s, Shriek Hollow returns to IDLE and can be bypassed without combat.

## Spawn Rules

| Rule | Value |
|---|---|
| Spawn chance | 5% per hour of room being unvisited |
| Max per zone | 2 active Apex at any time |
| Spawn announcement | Distant roar/rumble audible 3 rooms away |
| Room marking | Apex rooms have unique ambient particles (visible before entry) |
| Despawn | Never despawns once spawned (persists until killed or session ends) |
| Ecosystem effect | Apex presence clears all Tier 2 from room (killed or fled) |

## Multiplayer Scaling

| Players | HP Multiplier | Damage Multiplier |
|---|---|---|
| 1 | 1.0x | 1.0x |
| 2 | 1.6x | 1.0x |
| 3 | 2.2x | 1.1x |
| 4 | 2.8x | 1.2x |

## Implementation Notes

- Phase transitions use `ApexPhaseController` component with HP threshold callbacks
- Transition invulnerability implemented via `DamageImmunityBuffer` (120 ticks / 2s)
- Vein Warden root nodes are separate `NetworkObject`s with their own HP pool
- Shriek Hollow audio detection uses the shared Noise System (see NOISE_SYSTEM.md)
- Apex enemies have NavMesh priority 80 (highest among non-boss enemies)
- Loot drops are server-authoritative; Apex loot uses a dedicated loot table with pity timer
