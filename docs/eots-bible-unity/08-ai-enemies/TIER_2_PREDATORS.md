# Tier 2: Predators (Elite Enemies)

## Overview

Tier 2 enemies are mid-threat encounters that use behavior trees with utility scoring. They hunt Tier 1 creatures, attack players on sight, and retreat when wounded. Each type teaches a specific combat lesson.

## Shared Tier 2 Properties

| Property | Value |
|---|---|
| HP range | 8-15 masks |
| Damage | 2 masks per hit |
| AI type | Behavior Tree with Utility Scoring |
| Count per room | 1-3 |
| Respawn rule | 50% of original count on room re-entry |
| Loot | Uncommon Silk (80%), Rare Silk (15%), Infection +2 per kill (100%) |
| Ecosystem role | Predator of Tier 1; prey for Tier 3 |

## Utility Scoring (Target Selection)

Tier 2 enemies evaluate targets each tick using a weighted utility function:

```
Utility(target) = W_proximity * (1 - dist/max_dist)
                + W_threat    * threat_level(target)
                + W_hunger    * hunger_level
                + W_hp        * (1 - current_hp/max_hp)
```

| Weight | Value | Description |
|---|---|---|
| W_proximity | 0.35 | Closer targets score higher |
| W_threat | 0.25 | Players score higher than Tier 1 |
| W_hunger | 0.25 | Increases over time without a kill (drives Tier 1 hunting) |
| W_hp | 0.15 | Low HP shifts priority to weaker targets / flee |

Threat levels: Player = 1.0, Tier 1 = 0.3, Other Tier 2 = 0.0 (no infighting).

## Behavior Tree Structure (Shared Root)

```
[Selector]
  ├─ [Sequence: Flee]       → HP < 25% AND player within 10u → move away
  ├─ [Sequence: Hunt Player] → player detected → approach → attack
  ├─ [Sequence: Hunt Tier 1] → hunger > 50 AND Tier 1 visible → pursue → kill → feed
  └─ [Sequence: Patrol]     → follow patrol path → idle at waypoint → continue
```

## Enemy Types

### 1. Chitin Knight

| Property | Value |
|---|---|
| HP | 12 masks |
| Speed | Walk: 3 u/s, Charge: 7 u/s |
| Detection range | 12 units (LOS) |
| Attacks | Sword slash (2 masks, 0.4s), Shield bash (1 mask + stagger, 0.6s) |
| Block | Raises shield: 80% frontal damage reduction for 2s, 4s cooldown |
| Parryable | Yes. Shield bash has 0.2s parry window (flash cue). Successful parry staggers for 1.5s. |
| Lesson | Teaches parry timing and flanking |
| Spawn zones | 1, 4, 6 |

**Behavior Tree Extensions:**
- After 3 consecutive blocked attacks, performs a grab (unblockable, 3 mask damage)
- If player circles behind, pivots with shield bash (punishes predictable flanking)

### 2. Bloom Sniper

| Property | Value |
|---|---|
| HP | 8 masks |
| Speed | Walk: 2 u/s, Retreat: 5 u/s |
| Detection range | 20 units (LOS required, longest detection in Tier 2) |
| Attacks | Thorn shot (2 masks, 0.8s charge, hitscan), Scatter shot (1 mask x3, 5-unit spread) |
| Retreat trigger | Player within 6 units |
| Lesson | Teaches gap-closing and projectile dodging |
| Spawn zones | 2, 5 |

**Behavior Tree Extensions:**
- Prefers elevated positions (utility bonus for high-ground waypoints)
- If retreat path blocked, switches to scatter shot (panic fire)
- Will not shoot through Tier 1 allies (repositions for clear LOS)

### 3. Rot Hulk

| Property | Value |
|---|---|
| HP | 15 masks |
| Speed | Walk: 1.5 u/s, Charge: 6 u/s |
| Detection range | 8 units (LOS or noise > 20) |
| Attacks | Overhead slam (2 masks, 1s wind-up, AoE 2-unit radius), Charge (2 masks, 4-unit dash) |
| Armor | 30% passive damage reduction (all sources) |
| Stagger threshold | 8 masks of damage in 3s to stagger (1.5s vulnerability) |
| Lesson | Teaches patience, dodge timing on telegraphed attacks, stagger mechanic |
| Spawn zones | 3, 6 |

**Behavior Tree Extensions:**
- Charge triggers when player is 6-10 units away (gap closer)
- After charge, 2s recovery (vulnerable)
- If staggered, next attack after recovery is always overhead slam (punish greedy players)

### 4. Myco-Lurker

| Property | Value |
|---|---|
| HP | 10 masks |
| Speed | Walk: 4 u/s (fastest Tier 2), Stealth: 2 u/s |
| Detection range | 10 units (LOS or noise > 15) |
| Attacks | Ambush strike (3 masks from stealth, 0.3s), Claw swipe (2 masks, 0.4s) |
| Stealth | Invisible when on walls/ceilings and stationary for 2s. Shimmer visible within 4 units. |
| Lesson | Teaches environmental awareness and noise management |
| Spawn zones | 4, 7 |

**Behavior Tree Extensions:**
- Re-enters stealth after disengaging for 5s (returns to wall)
- Prioritizes ambush from behind (circles around player via wall paths)
- If ambush fails (player dodges), switches to aggressive claw pattern (no retreat)
- Will hunt Tier 1 from stealth (instant kill on Tier 1 from ambush)

### 5. Spore Shaman

| Property | Value |
|---|---|
| HP | 9 masks |
| Speed | Walk: 2.5 u/s |
| Detection range | 14 units |
| Attacks | Infection bolt (1 mask + 5 Infection, ranged, 1s cooldown), Heal pulse (heals nearby Tier 1/2 for 2 masks, 8s cooldown) |
| Support role | Buffs and heals other enemies within 8 units |
| Lesson | Teaches target prioritization (kill the healer first) |
| Spawn zones | 5, 8 |

**Behavior Tree Extensions:**
- Stays behind other enemies (maintains 6-unit minimum distance from player)
- Heal pulse prioritizes lowest-HP ally
- If alone (no allies in 10 units), flees toward nearest enemy group
- Death effect: explodes in Infection cloud (+3 Infection, 4-unit radius)

## Ecosystem Behavior

| Behavior | Detail |
|---|---|
| Tier 1 hunting | Tier 2 hunts Tier 1 when hunger > 50. Kill restores hunger to 0. |
| Hunger rate | +10 hunger per minute. At 100, Tier 2 becomes aggressive to all targets. |
| Feeding animation | 3s feed on Tier 1 corpse. Vulnerable during feed. |
| Territory | Each Tier 2 has a patrol zone (10-unit radius). Does not leave unless chasing. |
| Tier 2 vs Tier 2 | No infighting. Ignore each other. |
| Retroactive sim | If room unvisited 10+ min, each Tier 2 assumed to have killed 1 Tier 1 per 5 min. |

## Implementation Notes

- Behavior trees implemented using custom BT framework (see BEHAVIOR_TREES.md)
- Utility scoring evaluated every 30 ticks (500ms) to reduce CPU cost
- Stealth rendering (Myco-Lurker): shader-based dissolve with shimmer distortion effect
- Parry detection (Chitin Knight): hitbox active for exact 0.2s window, checked against player's parry input buffer
- All Tier 2 enemies use NavMeshAgent with obstacle avoidance priority 50 (higher than Tier 1's 30)
