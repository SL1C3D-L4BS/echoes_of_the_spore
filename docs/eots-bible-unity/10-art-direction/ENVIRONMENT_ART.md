# Environment Art

## Overview

Environment art in Echoes of the Spore follows the "Brutalist Bioluminescence" aesthetic: raw geometric architecture colonized by living fungal growth. Every surface negotiates between dead infrastructure and pulsing organic matter. All environments are 2D with parallax depth.

## Tileset Rules

### Tile Sizes

| Tile Category | Size | Usage |
|--------------|------|-------|
| Base terrain | 16x16 | Ground, walls, ceilings -- collision surfaces |
| Detail tiles | 32x32 | Surface decoration, cracks, moss, fungal growth |
| Hero pieces | 64x64 | Focal point elements: doorways, pillars, machinery, mural frames |
| Mega props | 128x128+ | Zone-specific set pieces (single instance per room) |

### Auto-Tiling

- Base terrain uses Unity Rule Tiles for seamless auto-tiling
- 47-tile blob tileset per zone (full marching squares coverage)
- Each zone requires: ground, wall, ceiling, platform, and hazard tilesets
- Transition tiles between zone types for Transition rooms

### Tileset Per Zone

| Zone | Ground Material | Wall Material | Accent Elements |
|------|----------------|--------------|-----------------|
| 1 Sporehaven | Soft earth, root-threaded | Organic curves, no edges | Glowing spore nodes, hanging roots |
| 2 Cortex | Neural tissue, pulsing | Synaptic membranes, ridged | Flickering nerve clusters, eye-like nodes |
| 3 Ferment Pits | Corroded metal grating | Stained pipes, dripping | Acid pools, vat fragments, bubble columns |
| 4 Chitinhall | Hexagonal chitin plates | Honeycomb walls, layered | Structural buttresses, collapsed segments |
| 5 Rot Garden | Tilled soil, mulch | Terraced stone, overgrown | Flower beds, irrigation channels, wilted blooms |
| 6 Myco-Forge | Scorched stone, riveted | Industrial metal, heat-warped | Anvils, crucibles, conveyor segments |
| 7 Necroveil | Void-touched stone | Impossible angles, recursive | Reality tears, floating fragments |
| 8 Core | Organic membrane | Breathing walls, veined | Pulsing nodes, nerve endings, capillary networks |

## Parallax Layers

Each zone uses 5 parallax layers for depth perception.

| Layer | Scroll Rate | Content | Resolution |
|-------|------------|---------|------------|
| Foreground (0) | 1.0 (camera-locked) | Interactive tiles, entities | 16px tiles |
| Midground (1) | 0.8 | Decorative pillars, non-interactive structures | 32px detail |
| Background 1 (2) | 0.5 | Zone-specific scenery (cavern walls, machinery) | 64px painted |
| Background 2 (3) | 0.3 | Atmospheric depth (distant structures, fog) | 128px painted |
| Background 3 (4) | 0.1 | Far horizon (void, distant glow, sky stand-in) | Full-screen painted |

### Parallax Rules

| Rule | Detail |
|------|--------|
| No foreground parallax clipping | Midground elements never overlap with interactive foreground |
| Background continuity | Backgrounds tile seamlessly for horizontal scrolling |
| Vertical parallax | Disabled for layers 3-4 (horizontal only) |
| Layer sorting | SpriteRenderer sortingOrder per layer, 100-unit gaps |
| Camera bounds | Parallax calculated from camera center, clamped to room bounds |

## Hero Piece Placement

Hero pieces are large, unique environmental elements that anchor room identity.

| Zone | Hero Piece Examples | Placement Rules |
|------|-------------------|----------------|
| 1 Sporehaven | Giant root archway, bioluminescent tree | Max 1 per room, centered or framing entrance |
| 2 Cortex | Synapse junction node, neural cluster | Near puzzle elements, pulsing with zone rhythm |
| 3 Ferment Pits | Massive distillation column, broken vat | Adjacent to acid hazards, leaking particles |
| 4 Chitinhall | Collapsed honeycomb tower, chitin throne | Mid-room or blocking paths, structural emphasis |
| 5 Rot Garden | Ancient greenhouse frame, colossal flower | Central garden feature, bloom/decay animation |
| 6 Myco-Forge | Giant anvil, dormant smelter | Near conveyor hazards, emitting heat haze |
| 7 Necroveil | Reality fracture pillar, veil membrane | Off-center, creates unease, geometry distortion |
| 8 Core | Nerve cluster, beating organ | Centered, pulses with ambient audio |

### Placement Constraints

| Constraint | Value |
|-----------|-------|
| Max hero pieces per room | 1 (Combat, Puzzle), 2 (Boss), 0 (Transition corridors) |
| Minimum distance from doors | 5 tiles |
| Collision | Hero pieces are non-collidable background elements unless specified |
| Z-depth | Placed on Midground layer (scroll rate 0.8) |

## Lighting Zones

Each room is divided into lighting zones that control atmosphere.

| Lighting Zone | Purpose | Light Source |
|--------------|---------|-------------|
| Entry corridor | Low ambient, draws player inward | Distant glow from main room |
| Main room center | Full illumination, gameplay clarity | Zone-specific ambient + point lights |
| Alcoves / side paths | Dim, mysterious, rewards exploration | Single point light, warm tint |
| Hazard areas | Warning coloration, danger | Hazard-colored emissive (acid green, lava red) |
| Mural locations | Spotlight on mural, draws attention | Focused spot light, Necrospore Gold tint |
| Boss arena | Dynamic, phase-responsive | Multiple point lights controlled by boss state |

### Light Implementation

| Parameter | Value |
|-----------|-------|
| Light type | 2D Point Light (URP 2D Renderer) |
| Max lights per room | 12 (perf budget) |
| Shadow casting | Foreground tiles only. No entity shadows. |
| Global light | One per zone, sets base ambient. Intensity 0.15-0.4 depending on zone. |
| Light cookies | Used for window patterns, grating shadows, fungal growth shadows |

## Atmosphere Guidelines

| Zone | Fog Density | Fog Color | Particle Atmosphere | Color Grading |
|------|------------|-----------|-------------------|---------------|
| 1 | Light (0.1) | Warm white | Floating spores, gentle | High saturation, warm |
| 2 | Light (0.15) | Cool blue | Synaptic sparks | Desaturated, blue shift |
| 3 | Medium (0.3) | Yellow-green | Acid drips, bubbles | Green tint, high contrast |
| 4 | Medium (0.25) | Amber | Chitin dust | Warm amber, low contrast |
| 5 | Variable | Shifts warm/cool | Petals + spores | Alternating saturation |
| 6 | Heavy near forges (0.4) | Orange | Embers, heat shimmer | Orange tint, high contrast |
| 7 | Heavy (0.5) | Near-black | Void motes | Near-grayscale, very low |
| 8 | Variable | Multi-colored | Heartbeat pulses | All colors, reactive |

## Implementation Notes

- Tilesets stored in `Assets/_Project/Art/Tilesets/{zone}/` with Rule Tile assets
- Parallax managed by `ParallaxController` reading camera delta per frame
- Hero pieces are prefab variants per zone in `Assets/_Project/Art/HeroPieces/`
- Lighting uses URP 2D Light components with `LightingZoneController` for room-specific presets
- Fog implemented as full-screen overlay sprite with per-zone material (not Unity fog)
- Color grading per zone via URP Volume profiles in `Assets/_Project/Rendering/Volumes/`
- Room activation (`SetActive`) handles light and parallax enable/disable for performance
