# Asset Tools

## Asset Pipeline Overview

```
Source Assets (Aseprite, WAV, FBX)
  → Import into Unity (TextureImporter, AudioImporter)
  → Process (atlas packing, compression)
  → Bundle via Addressables
  → Build into platform-specific asset bundles
  → Deploy with game build or patch independently
```

## Sprite Atlas Configuration

| Atlas | Contents | Max Size | Format |
|-------|----------|----------|--------|
| Player_Atlas | All player character sprites (4 classes) | 4096x4096 | ASTC 6x6 / BC7 |
| Enemy_Atlas_{zone} | Zone-specific enemy sprites (1 per zone) | 2048x2048 | ASTC 6x6 / BC7 |
| Tileset_{zone} | Zone tileset (1 per zone) | 4096x4096 | ASTC 4x4 / BC7 |
| UI_Atlas | All UI elements, icons, HUD | 2048x2048 | ASTC 6x6 / BC7 |
| VFX_Atlas | Particle sprites, hit effects | 2048x2048 | ASTC 6x6 / BC7 |
| Boss_Atlas_{id} | Per-boss sprite sheets | 2048x2048 | ASTC 6x6 / BC7 |
| NPC_Atlas | Hub NPCs, Community Tree | 2048x2048 | ASTC 6x6 / BC7 |

## Texture Import Settings

| Property | 2D Sprites | UI | Tilemaps |
|----------|-----------|-----|---------|
| Filter Mode | Point (no filter) | Bilinear | Point |
| Compression | Platform default | Platform default | Platform default |
| Max size | 4096 | 2048 | 4096 |
| Sprite mode | Multiple | Single | Multiple |
| Pixels per unit | 16 | 100 | 16 |
| Read/Write | Disabled | Disabled | Disabled |
| Mip maps | Disabled | Disabled | Disabled |
| sRGB | Yes | Yes | Yes |

## Addressables Configuration

| Group | Load Strategy | Unload Strategy | Contents |
|-------|-------------|----------------|----------|
| Core | Load on boot, never unload | Never | Player sprites, UI, core SFX |
| Zone_{1-8} | Load on zone entry | Unload on zone exit | Tilesets, enemies, ambience |
| Boss_{1-8} | Load on boss room entry | Unload on boss death | Boss sprites, boss music |
| Hub | Load on hub entry, keep warm | Never unload | NPC, tree, hub music |
| Narrator | Stream on demand | Auto-unload after line | VO clips |

## Sprite Animation Tool

Custom editor for Aseprite-to-Unity animation workflow:

| Feature | Description |
|---------|-------------|
| Import | Drag `.ase` file into Unity, auto-slices frames |
| Frame timing | Read Aseprite frame durations, apply to AnimationClip |
| Tag mapping | Aseprite tags → Unity AnimationClip names |
| Collision frames | Mark attack hitbox active frames in editor |
| Preview | Play animation in Scene view at game speed |
| Batch re-import | Re-import all sprites on source art change |

### Animation Naming Convention

```
{character}_{action}_{direction}.anim

Examples:
  bind_idle_right.anim
  bind_attack_light_right.anim
  bind_dash_right.anim
  bind_jump_right.anim
  sporeling_walk_left.anim
  boss_rot_knight_phase2_attack.anim
```

## Asset Validation Rules

| Rule | Check | Severity |
|------|-------|----------|
| Sprite POT | Texture dimensions are power-of-2 | Warning |
| Atlas overflow | Atlas does not exceed max size | Error |
| Missing reference | No broken sprite/prefab references | Error |
| Audio sample rate | All audio at 48 kHz | Error |
| Duplicate asset | No identical files with different names | Warning |
| LFS tracked | Binary assets are in .gitattributes | Error |
| Naming convention | Matches `{type}_{zone}_{variant}` pattern | Warning |
| Max file size | Individual asset < 50 MB | Warning |

## Asset Report (Editor Tool)

| Menu | Window > EotS > Asset Report |
|------|------|
| Total asset count | By type (sprite, audio, prefab, scriptable object) |
| Total disk size | Raw and compressed |
| Unused assets | Referenced by nothing in build |
| Largest assets | Top 20 by file size |
| Missing references | Broken links in prefabs/scenes |
| LFS compliance | Files that should be LFS-tracked but are not |

## Prefab Standards

| Prefab Type | Naming | Location |
|------------|--------|----------|
| Room templates | `Room_{zone}_{type}_{variant}.prefab` | `Assets/Rooms/{zone}/` |
| Enemy prefabs | `Enemy_{type}.prefab` | `Assets/Enemies/` |
| Boss prefabs | `Boss_{name}.prefab` | `Assets/Bosses/` |
| VFX prefabs | `VFX_{effect}.prefab` | `Assets/VFX/` |
| UI panels | `UI_{panel_name}.prefab` | `Assets/UI/` |
| Projectiles | `Proj_{type}.prefab` | `Assets/Projectiles/` |

## Build Size Budget

| Category | Budget | Notes |
|----------|--------|-------|
| Sprites (all atlases) | 200 MB | Compressed on-disk |
| Audio (FMOD banks) | 120 MB | See AUDIO_BUDGETS.md |
| Scenes | 20 MB | Hub + zone scenes |
| Prefabs | 10 MB | Room templates, enemies |
| Scripts (IL2CPP) | 30 MB | Compiled assemblies |
| Rust native plugin | 5 MB | Proc-gen .dll/.so |
| Shaders | 10 MB | URP 2D compiled variants |
| Misc (fonts, config) | 5 MB | — |
| **Total** | **~400 MB** | **Install size target** |
