# 10 — ART DIRECTION & VISUAL LANGUAGE

## Aesthetic: "Brutalist Bioluminescence"
Brutalist architecture (raw, geometric, industrial) colonized by bioluminescent organic forms (glowing, pulsing, alive). Every surface is a negotiation between dead infrastructure and living fungal matter.

## Color Palette
| Name | Hex | Usage |
|---|---|---|
| Void Black | #0A0A0F | Backgrounds, UI frames |
| Mycelium Charcoal | #1A1A2E | Secondary backgrounds |
| Spore White | #E8E4D9 | Text, clean UI |
| Necrospore Gold | #C9A84C | Accent, lore, rewards |
| Infection Cyan | #00FFD1 | Bioluminescence, health |
| Bloom Green | #2ECC71 | Verdant boons, nature |
| Scorch Red | #E74C3C | Pyreth boons, danger |
| Corrode Orange | #D4731A | Ferrox boons, decay |
| Glacine Blue | #3498DB | Ice, crystal |
| Void Purple | #6C3483 | Umbral boons, shadow |
| Harmonic Pink | #E91E90 | Resonant boons, sound |

## Character Sprites
- Resolution: 128×128 sprite sheet per animation state, 2x render for 4K
- Style: Hand-drawn line art, flat color fills, silhouette readable at 32px
- Infection tiers: 5 overlay variants showing progressive fungal mutation
- Animation: 12 frames per action, 60fps playback with interpolation
- Fused entities: composite sprite assembled from both partners + unique fusion overlays

## Environment Art
- Parallax: 4-5 layers per zone (foreground, midground, 3 background layers)
- Tiles: 16×16 base, 32×32 detail, 64×64 hero pieces
- Fossil Murals: 512×256 minimum, hand-painted, 3-5 per zone
- Mural reveal: SDF-based distance shader, reveals as player approaches

## Shaders
### Infection Pulse Shader
Parameters: InfectionLevel (0.0-1.0), PulseSpeed (1-3 Hz), EmissiveColor, GrowthMask (Texture2D)
- Tier 0-10: bypassed
- Tier 11-50: subtle emissive pulse on vein patterns
- Tier 51-75: strong pulse + GrowthMask at 50% opacity, Perlin noise modulation
- Tier 76-100: full emissive + 100% GrowthMask + particle emission

### Fossil Mural Reveal Shader
SDF distance from player position. Black → silhouette → full detail over 5-unit approach distance.
