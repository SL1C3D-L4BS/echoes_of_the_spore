# Level Editor

## Overview

Custom Unity Editor tooling for designing room templates, placing enemies, hazards,
and loot. Works alongside the Rust proc-gen system which assembles rooms into zones.

## Editor Windows

### Room Template Editor

| Feature | Implementation |
|---------|---------------|
| Location | `Editor/RoomTemplateEditor.cs` |
| Access | Window > EotS > Room Template Editor |
| Function | Custom inspector for room prefab Tilemaps |
| Layer painting | Foreground, Background, Collision, Hazard |
| Door placement | Snap-to-grid door connectors (N/S/E/W) |
| Enemy spawn points | Drag-drop spawn markers with enemy type selector |
| Loot spawn points | Rarity-weighted loot nodes |
| Validation | Checks connectivity, collision coverage, door alignment |

### Room Template Layers

| Layer | Tilemap | Purpose | Z-Order |
|-------|---------|---------|---------|
| Background | BG_Tilemap | Visual backdrop | -10 |
| Midground | MG_Tilemap | Parallax objects | -5 |
| Foreground | FG_Tilemap | Main terrain | 0 |
| Collision | Col_Tilemap | Physics colliders (invisible) | N/A |
| Hazard | Haz_Tilemap | Damage zones (acid, spikes) | 1 |
| Decoration | Deco_Tilemap | Non-colliding detail | 2 |

### Room Validation Rules

| Rule | Check | Severity |
|------|-------|----------|
| All doors reachable | Pathfind from each door to all others | Error |
| No floating terrain | All collision tiles connected to ground | Warning |
| Spawn points accessible | Pathfind from spawn to nearest door | Error |
| Min room size | >= 20x12 tiles | Error |
| Max room size | <= 60x40 tiles | Warning |
| Hazard coverage | < 30% of floor area | Warning |
| Loot node count | 1-5 per room | Warning |
| Enemy spawn count | Matches room difficulty tier | Info |

## Zone Visualizer

| Feature | Description |
|---------|-------------|
| Location | `Editor/ZoneVisualizer.cs` |
| Access | Window > EotS > Zone Visualizer |
| View | Graph view of proc-gen room layout |
| Node display | Room type, dimensions, door connections |
| Color coding | Green=start, Red=boss, Blue=normal, Yellow=treasure |
| Seed input | Enter seed to regenerate zone layout |
| Stats panel | Room count, total area, path lengths |
| Export | Save layout as JSON for debugging |

## Tileset Management

| Zone | Tileset Name | Tile Count | Palette |
|------|-------------|-----------|---------|
| 1 Sporehaven | sporehaven_tiles | 120 | Greens, bioluminescent |
| 2 Cortex | cortex_tiles | 110 | Purple, neural patterns |
| 3 Fermentation | fermentation_tiles | 130 | Amber, bubbling |
| 4 Chitinhall | chitinhall_tiles | 115 | Dark brown, chitinous |
| 5 Rot Garden | rotgarden_tiles | 125 | Decay colors, overgrown |
| 6 Myco-Forge | mycoforge_tiles | 140 | Metal, steam, orange |
| 7 Necroveil | necroveil_tiles | 100 | Dark, minimal |
| 8 Core | core_tiles | 150 | All colors, chaotic |
| Hub Canopy | canopy_tiles | 80 | Warm, organic |

## Enemy Placement Tool

```
Enemy Spawn Marker Properties:
  - Enemy Type: dropdown (all enemy types)
  - Count: 1-8 (number to spawn)
  - Difficulty Tier: 1-5 (scales with Heat)
  - Patrol Path: optional waypoint list
  - Trigger: OnRoomEnter / OnProximity / OnTimer
  - Spawn Delay: 0-5s after trigger
```

| Difficulty Tier | Enemy HP Modifier | Damage Modifier | Spawn Budget |
|----------------|------------------|----------------|-------------|
| 1 | 1.0x | 1.0x | 100 points |
| 2 | 1.3x | 1.2x | 150 points |
| 3 | 1.6x | 1.4x | 200 points |
| 4 | 2.0x | 1.6x | 275 points |
| 5 | 2.5x | 2.0x | 350 points |

## Boon Balance Inspector

| Feature | Description |
|---------|-------------|
| Location | `Editor/BoonBalanceWindow.cs` |
| View | Spreadsheet of all boons with stats |
| Columns | Name, Rarity, Damage, Cooldown, Duration, Synergy Tags |
| Sorting | Click column header to sort |
| Filtering | By rarity, class, synergy tag |
| Inline edit | Double-click cell to modify values |
| Export | CSV export for external balancing tools |

## Infection Curve Preview

| Feature | Description |
|---------|-------------|
| Location | `Editor/InfectionCurve.cs` |
| View | AnimationCurve showing infection progression |
| X-axis | Game time (minutes) |
| Y-axis | Infection level (0-100) |
| Overlays | Tier boundaries, stat multiplier curves |
| Presets | Slow (casual), Normal, Fast (speedrun) |
| Simulation | Play button to animate progression |

## Workflow: Creating a New Room Template

```
1. Open Room Template Editor
2. Select zone tileset
3. Paint collision layer (define room shape)
4. Paint foreground layer (terrain art)
5. Place doors (min 2, max 4)
6. Place enemy spawn markers
7. Place loot nodes (optional)
8. Place hazards (optional)
9. Run validation (fix errors)
10. Save as prefab: Assets/Rooms/{zone}/Room_{type}_{variant}.prefab
11. Register in RoomDatabase ScriptableObject
```
