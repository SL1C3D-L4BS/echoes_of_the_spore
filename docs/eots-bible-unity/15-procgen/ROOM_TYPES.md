# Room Types

## Overview

Seven room types define the gameplay experience within each zone. Each type has specific generation rules, size constraints, and content requirements. Room types are assigned during zone graph generation based on weighted probabilities per zone (see PROCGEN.md weight table).

## Room Type Summary

| Type | Purpose | Combat | Loot | Size Range |
|------|---------|--------|------|-----------|
| Combat | Core gameplay, enemy encounters | Yes (mandatory) | Enemy drops only | 50x30 - 80x40 |
| Puzzle | Environmental puzzles, optional combat | Optional (guards) | Puzzle reward | 40x24 - 60x36 |
| Reward | Boon selection, loot chests | No (cleared before entry) | Boon + loot | 40x24 - 50x30 |
| Rest | Recovery, NPC interaction | No | Minor consumables | 40x24 - 50x28 |
| Boss | Zone boss encounter | Yes (boss) | Boss loot table | 70x40 - 80x40 |
| Transition | Zone boundary corridor | No | None | 20x24 - 30x24 |
| Secret | Hidden rooms with rare content | Optional (trap/guard) | Rare loot, murals | 30x24 - 50x30 |

## Combat Rooms

### Generation Rules

| Parameter | Value |
|-----------|-------|
| Enemy count | 3-20 (scales with difficulty and room size) |
| Hazard count | 0-3 |
| Platform density | 0.20 - 0.35 (higher for aerial-heavy encounters) |
| Door lock | All doors locked until all enemies defeated |
| Wave spawning | Rooms with 10+ enemies use 2-3 wave spawns |
| Loot | Enemy drops only (no chests) |

### Combat Room Subtypes

| Subtype | Frequency | Special Rules |
|---------|-----------|---------------|
| Standard | 60% | Mixed enemy composition |
| Arena | 15% | Flat floor, no platforms, high enemy count |
| Gauntlet | 10% | Long horizontal room, enemies spawn in waves as player advances |
| Ambush | 10% | Enemies invisible until player reaches room center |
| Mini-boss | 5% | Single Tier 3 enemy + 2-4 Tier 1 adds |

## Puzzle Rooms

### Generation Rules

| Parameter | Value |
|-----------|-------|
| Puzzle type | Zone-specific (see table below) |
| Guard enemies | 0-3 (optional, do not respawn after puzzle solved) |
| Timer | Optional (30-90 seconds for timed puzzles) |
| Reward | Bonus loot chest on completion |
| Skip option | None (must solve or leave room via alternate path) |

### Zone-Specific Puzzles

| Zone | Puzzle Type | Mechanic |
|------|------------|----------|
| 1 Sporehaven | Root routing | Guide growing roots to connect power nodes |
| 2 Cortex | Pattern matching | Reproduce neural firing sequence on wall panels |
| 3 Ferment Pits | Fluid flow | Redirect acid flows using pipe segments |
| 4 Chitinhall | Structural support | Place chitin blocks to support collapsing ceiling |
| 5 Rot Garden | Bloom cycle | Trigger bloom/decay on plants in correct sequence |
| 6 Myco-Forge | Heat management | Route heat from forge to anvil via conveyor switches |
| 7 Necroveil | Perspective shift | Navigate rooms that change layout based on viewing angle |
| 8 Core | Signal relay | Transmit signal through Mycelium nerve nodes in order |

## Reward Rooms

### Generation Rules

| Parameter | Value |
|-----------|-------|
| Entry requirement | Previous room must be cleared (no enemies follow) |
| Layout | Template: central altar/pedestal, flanking decoration |
| Content | Boon selection (3 cards) + 1-2 loot chests |
| Enemy count | 0 |
| Hazard count | 0 |
| Door behavior | Open (no lock, player can leave without choosing) |
| Ambiance | Calm audio, bioluminescent lighting, spore particle effect |

### Reward Room Content Rules

| Content | Frequency | Details |
|---------|-----------|---------|
| Boon selection | 100% | Always present. 3 boons from zone-weighted pool. |
| Standard chest | 70% | Contains Echoes + 1 consumable |
| Rare chest | 25% | Contains Echoes + 1 equipment piece |
| Legendary chest | 5% | Contains Echoes + rare cosmetic or unique item |

## Rest Rooms

### Generation Rules

| Parameter | Value |
|-----------|-------|
| Layout | Template: campfire/rest area, bench/seat objects |
| Content | Rest point (full HP restore) + optional NPC |
| Enemy count | 0 (safe zone) |
| Hazard count | 0 |
| Infection | Infection paused while in Rest room |
| Duration limit | None (player can stay as long as they want) |

### Rest Room Features

| Feature | Availability | Effect |
|---------|-------------|--------|
| Rest point | Always | Full HP restore (1 use per visit) |
| Infection Purification | 50% of rest rooms | Reduce Infection by 5 (costs Echoes) |
| NPC vendor | 30% of rest rooms | Sell consumables for Echoes |
| Lore NPC | 20% of rest rooms | Non-interactive ambient character (civilization ghost) |

## Boss Rooms

### Generation Rules

| Parameter | Value |
|-----------|-------|
| Layout | Hand-authored arena per boss (not procedural) |
| Size | 70x40 minimum (larger bosses up to 80x40) |
| Entry | Boss gate (fog wall), one-way until boss defeated |
| Exit | Unlocked on boss death, leads to reward + zone transition |
| Hazards | Boss-specific (phase-dependent, see individual boss docs) |
| Platform layout | Fixed per boss, designed for boss moveset |

### Boss Arena Requirements

| Requirement | Detail |
|------------|--------|
| Flat fight space | Minimum 20x10 tile unobstructed floor area |
| Vertical space | Minimum 20 tiles ceiling height |
| Safe spawn | Player spawn point 10+ tiles from boss start position |
| Multiplayer spacing | 4 spawn points for co-op, distributed around arena edge |
| Phase markers | Visual indicators for phase transition zones |

## Transition Rooms

### Generation Rules

| Parameter | Value |
|-----------|-------|
| Layout | Narrow horizontal corridor, 8-12 tiles wide |
| Content | Zone blend tileset (source zone tiles fade to destination) |
| Enemy count | 0 |
| Hazard count | 0 |
| Gameplay | Walking only (no combat, abilities disabled) |
| Atmosphere | Crossfade audio, lighting, parallax between zones |
| Purpose | Pacing break, zone boundary marker, loading buffer |

## Secret Rooms

### Generation Rules

| Parameter | Value |
|-----------|-------|
| Entry | Hidden wall (breakable), Insight-revealed door, or puzzle-locked |
| Layout | Procedural but smaller than standard rooms |
| Content | At least one of: Fossil Mural, rare loot chest, unique encounter |
| Enemy count | 0-3 (guardian enemies, tougher than zone average) |
| Hazard count | 0-1 |
| Minimap | Not visible until discovered |
| Discoverability | Visual hints in adjacent rooms (cracks, glow, audio cue) |

### Secret Room Content Distribution

| Content | Probability | Notes |
|---------|------------|-------|
| Fossil Mural | 40% | Primary lore delivery for hidden murals |
| Rare loot chest | 35% | Contains equipment 1 tier above zone average |
| Unique encounter | 15% | Non-hostile NPC, civilization ghost, lore object |
| Community Tree node | 10% | Bonus Community Tree contribution point |

## Size Range Details

| Type | Width (tiles) | Height (tiles) | Area (tiles) |
|------|--------------|----------------|-------------|
| Combat (small) | 50 | 30 | 1,500 |
| Combat (large) | 80 | 40 | 3,200 |
| Puzzle | 40-60 | 24-36 | 960-2,160 |
| Reward | 40-50 | 24-30 | 960-1,500 |
| Rest | 40-50 | 24-28 | 960-1,400 |
| Boss | 70-80 | 40 | 2,800-3,200 |
| Transition | 20-30 | 24 | 480-720 |
| Secret | 30-50 | 24-30 | 720-1,500 |

## Implementation Notes

- Room type enum defined in `eots-procgen-core/src/types.rs` as `RoomType`
- Generation rules per type in `eots-procgen-zone/src/room_rules.rs`
- Hand-authored templates (Boss, Rest, Reward) stored as byte arrays in Rust, loaded at compile time
- Unity prefab lookup keyed by `(zone_id, room_type)` in `RoomPrefabDatabase` ScriptableObject
- Secret room visibility managed by `SecretRoomSystem` checking player Insight status and exploration state
- Room clear state tracked by `RoomClearComponent` on room entity, consumed by door lock system
