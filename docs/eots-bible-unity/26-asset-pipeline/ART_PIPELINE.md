# Art Production Pipeline

## Workflow

```
Concept (Sketch)
  → Line Art (Aseprite / Photoshop)
    → Color (Flat fills + shading)
      → Animation (Aseprite frame-by-frame)
        → Export (PNG sprite sheets)
          → Unity Import (Sprite Atlas, Rule Tiles)
            → Shader Application (Infection overlay, bioluminescence)
```

## Tools

| Stage | Tool | Format |
|-------|------|--------|
| Concept | Procreate / Photoshop | .psd |
| Pixel Art | Aseprite | .aseprite → .png |
| Tileset | Aseprite + Tiled | .aseprite → .png |
| Animation | Aseprite (frame-by-frame) | .aseprite → sprite sheet .png |
| VFX | Unity Particle System + Shader Graph | .prefab |
| Shaders | Unity Shader Graph (URP) | .shadergraph |
| UI Mockup | Figma | .fig → exported .png |

## Naming Convention

```
Characters:   chr_{class}_{crest}_{state}_{frame}.png
Enemies:      ene_{name}_{tier}_{state}_{frame}.png
Tiles:        tile_{zone}_{type}_{variant}.png
VFX:          vfx_{effect}_{frame}.png
UI:           ui_{category}_{element}_{state}.png
Murals:       mural_{zone}_{number}.png
```

## Sprite Atlas Organization

| Atlas | Contents | Max Size |
|-------|----------|----------|
| Atlas_Players | All 4 class sprites + Crests + Infection overlays | 4096×4096 |
| Atlas_Enemies_{Zone} | One per zone, all enemy sprites | 2048×2048 |
| Atlas_Tiles_{Zone} | One per zone, all tiles | 4096×4096 |
| Atlas_VFX | All particle textures | 2048×2048 |
| Atlas_UI | All UI elements | 2048×2048 |
| Atlas_Bosses | All 8 boss sprites | 4096×4096 |

## Git LFS Tracking

```
*.png filter=lfs
*.psd filter=lfs
*.aseprite filter=lfs
*.wav filter=lfs
*.ogg filter=lfs
*.bank filter=lfs
*.dll filter=lfs
```

## Quality Checklist (Per Asset)

- [ ] Correct resolution (16/32/64/128 per spec)
- [ ] Silhouette readable at 32px
- [ ] Color palette matches zone spec
- [ ] Animation smooth at 60fps
- [ ] Infection overlay layers present (5 tiers)
- [ ] Named per convention
- [ ] Added to correct Sprite Atlas
- [ ] No anti-aliasing artifacts (pixel art must be crisp)
