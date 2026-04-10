# HUD Layout Specification

## Screen Layout (1920×1080 Reference)

```
┌────────────────────────────────────────────────────────────┐
│ [HP Masks]  [Silk Gauge]                    [Infection %]  │
│ ♥♥♥♥♥       ████████░░                        [▲ 47%]     │
│                                                             │
│                                                             │
│                    ┌──────────┐                             │
│                    │ GAMEPLAY │                             │
│                    │  AREA    │                             │
│                    │          │                             │
│                    └──────────┘                             │
│                                                             │
│ [Combo: 12]                              [Community Tree]  │
│                                          ████░░░ Depth 8   │
│ [Active Functions]                                         │
│ [1][2][3]           [Minimap]            [Zone: Cortex]    │
└────────────────────────────────────────────────────────────┘
```

## HUD Elements

### Top-Left: Player Status
| Element | Size | Content |
|---------|------|---------|
| HP Masks | 32×32 per mask | Filled (white) / Empty (dark) / Overloaded (cracked) |
| Silk Gauge | 200×12 bar | Cyan fill, white border, pulses at 100% |
| Class Icon | 24×24 | Current class silhouette |

### Top-Right: Infection
| Element | Size | Content |
|---------|------|---------|
| Infection % | Text + icon | Percentage with color-coded tier |
| Tier indicator | Visual | Glowing ring, intensity = tier |

### Bottom-Left: Abilities
| Element | Size | Content |
|---------|------|---------|
| Active Functions 1-3 | 48×48 each | Icon + cooldown radial overlay |
| Combo counter | Text | Appears on hit, fades 2s after last hit |

### Bottom-Right: World State
| Element | Size | Content |
|---------|------|---------|
| Community Tree bar | 160×8 | Current branch progress |
| Zone name | Text | Fade in on zone transition |
| Minimap | 128×128 | Revealed rooms, current position, fog of war |

### Center (Contextual)
| Element | Trigger | Duration |
|---------|---------|----------|
| Boon selection UI | Reward room | Until chosen |
| Damage numbers | On hit | 1.5s float |
| Boss HP bar | Boss room | Entire fight |
| Infection tier popup | Tier crossing | 3s |
| Death text | Overload | 2s |
| Narrator subtitle | Mycelium Voice line | Duration of line |

## Fusion HUD Modifications

When fused, HUD expands:
- Both players' HP visible (stacked)
- Role indicator: "PILOT" / "GUNNER" / "SUPPORT" / "RELAY"
- Spore Plan gauge added (fills over 60s)
- Partner's Infection visible
- Active Functions show combined pool

## Design Rules
1. **Minimal by default** — hide elements when not relevant (combo counter fades, minimap fades in dungeon)
2. **No text walls** — icons and bars over numbers
3. **Accessibility** — all info also available via audio cues
4. **Colorblind safe** — icons + shapes supplement color coding
5. **Scale with resolution** — anchor-based UI, not pixel-fixed
6. **Input-agnostic** — show controller OR keyboard prompts, auto-detect
