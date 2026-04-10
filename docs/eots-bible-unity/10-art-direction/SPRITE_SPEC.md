# Sprite Specification

## Character Sprites

| Property | Value |
|----------|-------|
| Base resolution | 128×128 per frame |
| 4K render | 256×256 (2x supersample) |
| Art style | Hand-drawn line art, flat color fills |
| Silhouette test | Must be readable at 32px height |
| Animation framerate | 12 key frames, 60fps playback with interpolation |
| Color depth | 32-bit RGBA |
| Format | PNG (source), Unity Sprite Atlas (runtime) |

## Animation States

| State | Frames | Loop |
|-------|--------|------|
| Idle | 8 | Yes |
| Run | 8 | Yes |
| Jump_Up | 4 | No |
| Jump_Peak | 2 | Yes |
| Fall | 4 | No |
| Land | 3 | No |
| Dash | 4 | No |
| Light_1 | 6 | No |
| Light_2 | 6 | No |
| Light_3 | 8 | No |
| Heavy_Charge | 4 | Yes |
| Heavy_Release | 8 | No |
| Air_Light | 6 | No |
| Air_Heavy | 8 | No |
| Hit_Receive | 4 | No |
| Death | 12 | No |
| Heal_Bind | 8 | No |
| Ability_1 | 8-12 | No |
| Ability_2 | 8-12 | No |

## Infection Overlays (5 Tiers)

Each character has 5 progressive fungal mutation overlays:
1. **Tier 1 (11-25):** Faint vein pattern on limbs (alpha: 30%)
2. **Tier 2 (26-50):** Visible growths on shoulders/back (alpha: 60%)
3. **Tier 3 (51-75):** Major fungal formations, face partially covered (alpha: 80%)
4. **Tier 4 (76-90):** Nearly fully covered, glowing cracks (alpha: 90%)
5. **Tier 5 (91-100):** Fully mutated, original character barely visible (alpha: 100%)

Overlays are separate sprite layers composited at runtime. Emissive channel drives glow shader.

## Fused Entity Sprites

Fused entities are composited from partner sprites + unique overlays:
- Base: larger body frame (192×192)
- Partner elements visible (colors, silhouette hints)
- Unique VFX connecting partners (mycelial tendrils)
- 12 Fused classes each need full animation set

## Enemy Sprites

| Tier | Base Size | Animation Frames (Idle/Attack/Death) |
|------|-----------|--------------------------------------|
| Tier 1 | 64×64 | 4/4/4 |
| Tier 2 | 96×96 | 6/8/6 |
| Tier 3 | 128×128 | 8/12/8 |
| Boss | 256×256 to 512×512 | 8/16/12 |

## Tileset Specification

| Property | Value |
|----------|-------|
| Tile size | 16×16 base |
| Detail tiles | 32×32 |
| Hero pieces | 64×64 |
| Tilemap | Unity Tilemap component |
| Auto-tiling | Rule Tiles for terrain |
| Collision | Tilemap Collider 2D (disabled — our ECS handles collision) |

## Parallax Layers (Per Zone)

| Layer | Scroll Rate | Content |
|-------|------------|---------|
| Foreground | 1.0 (locked to camera) | Interactive elements, tiles |
| Midground | 0.8 | Decorative structures, pillars |
| Background 1 | 0.5 | Zone-specific scenery |
| Background 2 | 0.3 | Atmospheric elements |
| Background 3 | 0.1 | Distant horizon, sky/void |
