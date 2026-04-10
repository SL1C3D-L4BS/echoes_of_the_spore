# Rendering Performance

## Rendering Stack

| Component | Technology | Budget |
|-----------|-----------|--------|
| Pipeline | URP 2D Renderer | — |
| Sprites | SpriteRenderer + Sprite Atlas | 4.0ms total GPU |
| Tilemaps | Unity Tilemap + TilemapRenderer | Included in above |
| Lighting | URP 2D Lights (baked where possible) | 1.0ms |
| Particles | Particle System (GPU) | 0.5ms |
| Post-processing | URP Volume (bloom, vignette) | 0.5ms |
| UI | UGUI Canvas + TextMeshPro | 0.5ms |
| **Total GPU** | — | **< 6.5ms** |

## Draw Call Budget

| Category | Max Draw Calls | Batching Strategy |
|----------|---------------|------------------|
| Tilemap layers | 6 per room | Auto-batch per layer |
| Player sprites | 4 (one per player) | SRP Batcher |
| Enemy sprites | 32 (max enemies per room) | GPU Instancing |
| Projectiles | 16 | GPU Instancing |
| VFX/Particles | 8 | GPU particles |
| UI elements | 10 | Canvas batching |
| Parallax layers | 4 | SRP Batcher |
| 2D Lights | 8 | Light batching |
| **Total** | **< 100** | — |

## Sprite Batching Rules

| Rule | Implementation |
|------|---------------|
| Same atlas = same batch | All zone sprites in one atlas |
| Same material = same batch | Shared material per atlas |
| Same sorting layer = batch-compatible | Minimize sorting layers |
| Z-order changes break batches | Use sorting order within layers |
| Dynamic sprites (players, enemies) | GPU Instancing with MaterialPropertyBlock |

## Tilemap Optimization

| Optimization | Impact | Implementation |
|-------------|--------|---------------|
| Chunk-based rendering | Reduces draw calls | Unity default (32x32 chunks) |
| Off-screen culling | Reduces rendered tiles | Camera bounds + padding |
| Static batching for background | Zero CPU overhead | Mark BG tilemap as static |
| Compressed tile data | Reduce memory | Tile palette optimization |
| Minimize tilemap layers | Fewer draw calls | Merge decorative layers |

### Tilemap Layer Configuration

| Layer | Rendering | Shader | Notes |
|-------|-----------|--------|-------|
| Background | Static batch | Unlit | No lighting needed |
| Midground | Static batch | Lit 2D | Receives 2D light |
| Foreground | Static batch | Lit 2D | Main terrain |
| Hazard | Dynamic | Lit 2D + emission | Animated hazard indicators |
| Decoration | Static batch | Lit 2D | Non-colliding detail |

## 2D Lighting Budget

| Light Type | Max Count | Cost Each | Total Budget |
|-----------|-----------|-----------|-------------|
| Global Light | 1 | 0.1ms | 0.1ms |
| Point Lights (enemies/items) | 4 | 0.15ms | 0.6ms |
| Spot Lights (environmental) | 2 | 0.1ms | 0.2ms |
| Freeform Lights (area) | 1 | 0.1ms | 0.1ms |
| **Total** | **8** | — | **~1.0ms** |

### Light Optimization

| Technique | Savings |
|-----------|---------|
| Bake static lights where possible | 50% light cost |
| Limit light radius (< 10 units) | Reduces overdraw |
| Shadow casters: only foreground tilemap | Reduces shadow cost |
| Disable shadows on small/distant lights | 30% per light |

## Particle Budget

| VFX Type | Max Particles | Lifetime | Emission Rate |
|----------|-------------|----------|---------------|
| Hit impact | 20 | 0.3s | Burst: 20 |
| Dash trail | 10 | 0.5s | Continuous: 20/s |
| Spore ambient | 30 | 3.0s | Continuous: 10/s |
| Boss aura | 50 | 2.0s | Continuous: 25/s |
| Death explosion | 40 | 1.0s | Burst: 40 |
| Heal effect | 15 | 1.5s | Burst: 15 |
| **Max concurrent** | **~150** | — | — |

## Post-Processing

| Effect | Enabled | Settings | Cost |
|--------|---------|----------|------|
| Bloom | Yes | Threshold: 0.8, Intensity: 0.3 | 0.2ms |
| Vignette | Yes | Intensity: 0.2 | 0.05ms |
| Color Grading | Yes | Per-zone LUT | 0.1ms |
| Film Grain | No (disabled for pixel art) | — | — |
| Chromatic Aberration | Infection only (tier 51+) | Intensity: 0.0-0.5 | 0.1ms |
| Screen Shake | CPU-driven camera offset | — | ~0ms |

## Resolution Scaling

| Platform | Native Resolution | Render Scale | Upscale |
|----------|------------------|-------------|---------|
| PC 1080p | 1920x1080 | 1.0 | None |
| PC 4K | 3840x2160 | 1.0 | None (pixel art scales cleanly) |
| PS5 | 3840x2160 | 1.0 | None |
| Xbox Series X | 3840x2160 | 1.0 | None |
| Xbox Series S | 1920x1080 | 1.0 | None |
| Switch 2 (docked) | 1920x1080 | 1.0 | None |
| Switch 2 (handheld) | 1280x720 | 1.0 | None |
| Steam Deck | 1280x800 | 1.0 | None |

Pixel art renders at native integer multiples. No fractional scaling.

## Overdraw Analysis

| Worst Case Scenario | Overdraw | Acceptable? |
|-------------------|----------|-------------|
| 4 parallax layers + 6 tile layers + enemies | ~6x | Yes |
| Boss room (full VFX) | ~8x | Yes |
| Hub with Community Tree effects | ~5x | Yes |
| Target max overdraw | < 10x | — |

## Rendering Red Lines

| Metric | Red Line | Action |
|--------|----------|--------|
| Draw calls > 200 | P1 bug | Investigate batching breaks |
| GPU time > 8ms | P1 bug | Profile with RenderDoc |
| Overdraw > 12x | P2 bug | Reduce particle/layer count |
| SetPass calls > 20 | P1 bug | Consolidate materials |
| Vertex count > 50K | P2 bug | Unusual for 2D, investigate |
