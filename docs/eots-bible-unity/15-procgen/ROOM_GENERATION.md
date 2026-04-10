# Room Generation

## Overview

Room generation converts a `RoomData` node from the zone graph into a playable game space. Each room is defined by its type, dimensions, and seed, then populated with tiles, enemies, hazards, and interactables. The generation algorithm runs in Rust (deterministic), while instantiation happens in Unity.

## Tile Placement Algorithm

### Grid System

| Parameter | Value |
|-----------|-------|
| Tile unit | 16x16 pixels (1 Unity unit = 1 tile) |
| Room size range | Width: 40-80 tiles, Height: 24-40 tiles |
| Border | 2-tile thick solid border on all sides (collision walls) |
| Floor | Fill interior with zone ground tiles via Rule Tile auto-tiling |
| Platform density | 0.15 - 0.35 (fraction of interior tiles that are platforms) |

### Platform Generation

1. Divide room interior into a grid of 8x6 cells (each cell ~5-6 tiles).
2. For each cell, roll platform placement using room seed and density parameter.
3. Platform shapes selected from template library (L-shape, flat, step, floating).
4. Minimum 2-tile gap between platforms for player traversal.
5. At least one ground-connected path from entrance to exit guaranteed.

### Collision Grid

```
[W][W][W][W][W][W][W][W][W][W]
[W][.][.][.][.][.][.][.][.][W]
[W][.][P][P][P][.][.][.][.][W]
[W][.][.][.][.][.][P][P][.][W]
[W][.][.][.][.][.][.][.][.][W]
[W][G][G][G][G][G][G][G][G][W]
[W][W][W][W][W][W][W][W][W][W]

W = Wall (solid collision)
G = Ground (solid collision, walkable surface)
P = Platform (one-way collision, pass-through from below)
. = Air (no collision)
```

Collision data exported as a bit-packed byte array from Rust. Unity side creates Tilemap Collider from this data.

## Enemy Spawner Placement

### Spawner Rules

| Rule | Detail |
|------|--------|
| Min distance from door | 4 tiles (no ambush on entry) |
| Min distance between spawners | 3 tiles |
| Ground requirement | Grounded enemies spawn on solid tiles only |
| Aerial enemies | Can spawn anywhere in air space |
| Boss spawner | Always center of room, single spawner |
| Max spawners per room | Room enemy count (from `RoomData.EnemyCount`) |

### Spawner Placement Algorithm

1. Collect all valid spawn positions (ground tiles for grounded, air tiles for aerial).
2. Filter by minimum distance from doors and other spawners.
3. Select positions using weighted random (prefer positions with clear sight lines to player path).
4. Assign enemy type based on zone + room difficulty (see DIFFICULTY_SCALING.md).
5. Each spawner stores: position, enemy type, facing direction, activation trigger.

### Activation Triggers

| Trigger | Behavior |
|---------|----------|
| OnRoomEnter | Spawns when player enters room (default) |
| OnProximity | Spawns when player within 8 tiles |
| OnTimer | Spawns after N seconds in room (wave-based combat rooms) |
| OnBossPhase | Spawns during boss phase transition (boss rooms only) |

## Hazard Distribution

### Hazard Types Per Zone

| Zone | Primary Hazard | Secondary Hazard | Density |
|------|---------------|-----------------|---------|
| 1 Sporehaven | Spore clouds (slow) | Falling roots | 0.05 |
| 2 Cortex | Electric arcs | Input-reversal zones | 0.08 |
| 3 Ferment Pits | Acid pools | Dripping acid | 0.12 |
| 4 Chitinhall | Collapsing floor | Chitin spikes | 0.10 |
| 5 Rot Garden | Bloom/decay zones | Thorn walls | 0.08 |
| 6 Myco-Forge | Lava pools | Conveyor belts | 0.12 |
| 7 Necroveil | Void zones | Reality distortion | 0.10 |
| 8 Core | Infection pulses | Mycelial tendrils | 0.15 |

### Placement Rules

| Rule | Detail |
|------|--------|
| Never block critical path | Hazards may obstruct optional paths, never the only path |
| Adjacent to platforms | Acid/lava pools placed at ground level, near platform edges |
| Minimum safe zone | 3-tile hazard-free zone around each door |
| Telegraph range | Hazard visual effect extends 2 tiles beyond damage zone |
| Density scaling | Hazard density multiplied by room difficulty factor |
| Max hazards | `RoomData.HazardCount` (0-5, from zone graph) |

## Exit / Entrance Connections

### Door Placement

| Parameter | Value |
|-----------|-------|
| Door width | 3 tiles (enough for fused entities at 192px) |
| Door height | 4 tiles |
| Door positions | Left wall, right wall, top, bottom (matching edge connections) |
| Transition zone | 2-tile corridor extending from door into room interior |
| Lock indicator | Locked doors show barrier VFX until room clear (combat rooms) |

### Connection Types

| Type | Visual | Behavior |
|------|--------|----------|
| Standard | Open doorway | Always passable after room clear |
| Locked | Energy barrier | Requires all enemies defeated |
| Shortcut | Faint outline | Requires Community Tree unlock |
| Secret | Hidden wall | Revealed by Insight status or specific interaction |
| Boss gate | Fog wall | One-way entry, no exit until boss defeated |
| Transition | Zone portal | Leads to zone transition corridor |

## Room Templates

Certain room types use hand-authored template overlays combined with procedural content:

| Room Type | Template Usage | Procedural Elements |
|-----------|---------------|-------------------|
| Combat | Procedural layout | Enemies, hazards, platforms |
| Puzzle | Hand-authored puzzle structure | Enemy guards, reward placement |
| Reward | Small template (altar layout) | Boon selection, loot |
| Rest | Template (campfire area) | NPC placement, dialogue |
| Boss | Hand-authored arena per boss | Phase-specific spawners |
| Transition | Template (corridor) | Zone blend tiles |
| Secret | Procedural with hidden entry | Rare loot, mural placement |

## Implementation Notes

- Room layout generation runs in Rust crate `eots-procgen-zone` using `RoomData` from zone graph
- Tile data output as `RoomLayout` struct: collision grid (bit-packed), spawner list, hazard list, door positions
- Unity side: `RoomInstantiator` reads `RoomLayout` and creates Tilemap + child GameObjects
- Platform templates stored as byte arrays in Rust, selected by room seed
- Enemy spawner activation managed by `SpawnerActivationSystem` in ECS
- Hazard prefab lookup uses `HazardDatabase` ScriptableObject keyed by zone + hazard type
- Door barriers use `DoorBarrierComponent` with state machine: Locked -> Unlocked -> Open
