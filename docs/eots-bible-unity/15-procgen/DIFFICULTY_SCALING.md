# Difficulty Scaling

## Overview

Room difficulty determines enemy composition, hazard intensity, and loot quality. The difficulty value (0-100) is assigned during zone graph generation and modified at runtime by contextual factors: zone depth, Heat level, party size, and boon count.

## Base Difficulty Formula

```
base_difficulty = zone_base + (room_position_on_critical_path / critical_path_length) * zone_range
```

### Zone Difficulty Ranges

| Zone | Zone Base | Zone Range | Min Room Difficulty | Max Room Difficulty |
|------|-----------|-----------|--------------------|--------------------|
| 1 | 5 | 25 | 5 | 30 |
| 2 | 15 | 30 | 15 | 45 |
| 3 | 25 | 30 | 25 | 55 |
| 4 | 35 | 30 | 35 | 65 |
| 5 | 40 | 30 | 40 | 70 |
| 6 | 50 | 30 | 50 | 80 |
| 7 | 55 | 35 | 55 | 90 |
| 8 | 65 | 35 | 65 | 100 |

## Runtime Modifiers

### Heat Level

Heat is a player-selected difficulty multiplier (0-10) chosen at delve start.

| Heat | Difficulty Multiplier | Enemy HP Multiplier | Loot Quality Bonus |
|------|---------------------|--------------------|--------------------|
| 0 | 1.0x | 1.0x | +0% |
| 1 | 1.05x | 1.1x | +5% |
| 2 | 1.10x | 1.2x | +10% |
| 3 | 1.15x | 1.3x | +15% |
| 4 | 1.20x | 1.4x | +20% |
| 5 | 1.30x | 1.5x | +25% |
| 6 | 1.40x | 1.6x | +30% |
| 7 | 1.50x | 1.8x | +40% |
| 8 | 1.65x | 2.0x | +50% |
| 9 | 1.80x | 2.3x | +65% |
| 10 | 2.00x | 2.5x | +80% |

### Party Size Scaling

| Party Size | Enemy Count Multiplier | Enemy HP Modifier | Damage Modifier |
|-----------|----------------------|-------------------|----------------|
| 1 (solo) | 0.6x | 0.7x | 0.8x |
| 2 | 0.8x | 0.9x | 0.9x |
| 3 | 1.0x (baseline) | 1.0x | 1.0x |
| 4 | 1.2x | 1.15x | 1.1x |

### Boon Count Scaling

As players accumulate boons, they become more powerful. Rooms scale to compensate.

| Boons Held (Party Total) | Enemy HP Modifier | Enemy Damage Modifier |
|--------------------------|-------------------|-----------------------|
| 0-5 | 1.0x | 1.0x |
| 6-10 | 1.05x | 1.0x |
| 11-15 | 1.10x | 1.05x |
| 16-20 | 1.15x | 1.05x |
| 21-25 | 1.20x | 1.10x |
| 26-30 | 1.25x | 1.10x |
| 31+ | 1.30x | 1.15x |

## Effective Difficulty Calculation

```
effective_difficulty = base_difficulty * heat_multiplier
enemy_count = floor(base_enemy_count * party_size_multiplier)
enemy_hp = base_hp * party_hp_modifier * heat_hp_multiplier * boon_hp_modifier
enemy_damage = base_damage * party_damage_modifier * heat_difficulty * boon_damage_modifier
```

### Example: Zone 4, Room 6/10, Heat 3, Party 2, 12 Boons

```
base_difficulty = 35 + (6/10) * 30 = 53
effective_difficulty = 53 * 1.15 = 60.95 -> 61
enemy_count = 8 * 0.8 = 6 enemies
enemy_hp = 100 * 0.9 * 1.3 * 1.10 = 128.7 -> 129 HP
enemy_damage = 15 * 0.9 * 1.15 * 1.05 = 16.3 -> 16 damage
```

## Enemy Composition Selection

Difficulty determines the tier mix of enemies in a room.

### Tier Distribution by Difficulty

| Difficulty Range | Tier 1 % | Tier 2 % | Tier 3 % | Mycelium Hunter % |
|-----------------|---------|---------|---------|-------------------|
| 0-20 | 100 | 0 | 0 | 0 |
| 21-35 | 80 | 20 | 0 | 0 |
| 36-50 | 60 | 35 | 5 | 0 |
| 51-65 | 40 | 45 | 15 | 0 |
| 66-80 | 20 | 50 | 25 | 5 |
| 81-90 | 10 | 40 | 40 | 10 |
| 91-100 | 5 | 30 | 50 | 15 |

### Zone-Specific Enemy Types

Each zone has a pool of enemies per tier. Room seed selects from the pool.

| Zone | Tier 1 Types | Tier 2 Types | Tier 3 Types |
|------|-------------|-------------|-------------|
| 1 | Sporeling, Root Crawler | Bloom Stalker, Spore Drifter | Spore Mother Scion |
| 2 | Synapse Wisp, Nerve Tendril | Thought Eater, Arc Node | Neural Colossus |
| 3 | Acid Slime, Ferment Drone | Vat Guardian, Toxic Spewer | Acid Elemental |
| 4 | Chitin Worker, Hive Drone | Chitin Knight, Hive Sentinel | Chitin Behemoth |
| 5 | Rot Drifter, Petal Crawler | Blight Walker, Thorn Golem | Garden Titan |
| 6 | Cinder Spark, Slag Crawler | Forge Guard, Molten Smith | Forge Colossus |
| 7 | Void Mote, Shadow Wisp | Veil Stalker, Reality Shard | Void Reaver |
| 8 | Mycelial Tendril, Core Drone | Heart Guard, Neural Knight | Progenitor Shard |

## Infection Difficulty Interaction

Player Infection level affects difficulty perception but not room generation:

| Infection | Effect on Player | Difficulty Impact |
|-----------|-----------------|-------------------|
| 0-25 | No combat modifier | Rooms generated as normal |
| 26-50 | Player gains +5% damage from Infection bonuses | Offset by enemy scaling (neutral) |
| 51-75 | Player gains +10% damage, -5% defense | Slightly harder for player |
| 76-100 | Player gains +15% damage, -15% defense | Significantly harder for player |

Infection does not modify room generation. It modifies the player's combat effectiveness, creating an organic difficulty curve that the player manages through Purification or acceptance.

## New Game+ Scaling

| NG+ Cycle | Base Difficulty Offset | Enemy HP Multiplier | New Mechanics |
|-----------|----------------------|--------------------|----|
| NG+1 | +10 all zones | 1.5x | Tier 2 enemies gain new attack patterns |
| NG+2 | +15 all zones | 2.0x | Tier 3 enemies in Zone 1-2 |
| NG+3 | +20 all zones | 2.5x | Mycelium Hunters in all zones |
| NG+4+ | +25 (cap) | 3.0x (cap) | Boss phase 4 added |

## Implementation Notes

- Base difficulty assigned in Rust during zone graph generation (Phase 1)
- Runtime modifiers computed in Unity by `DifficultyScalingSystem` on room entry
- Heat level stored in `DelveConfigComponent` (set at delve start, immutable)
- Party size from `PartyComponent.MemberCount`
- Boon count from `BoonInventoryComponent.TotalBoons` (sum across party)
- Enemy stat modification applied via `EnemyStatModifierSystem` on spawner activation
- Effective difficulty exposed to AI Director for encounter pacing adjustments
- NG+ flag and cycle count stored in character save data
