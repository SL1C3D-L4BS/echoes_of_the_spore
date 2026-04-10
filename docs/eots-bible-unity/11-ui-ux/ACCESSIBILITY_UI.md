# Accessibility UI

## Overview

Echoes of the Spore targets WCAG AA compliance for all UI elements and provides comprehensive accessibility options. Accessibility is not an afterthought -- it is a first-class design constraint. All accessibility features are available from the first boot.

## Colorblind Modes

### Three Supported Types

| Mode | Condition | Affected Population | Palette Remapping |
|------|-----------|-------------------|-------------------|
| Deuteranopia | Red-green (green deficient) | ~5% of males | Green/Red elements remapped to Blue/Orange |
| Protanopia | Red-green (red deficient) | ~1.5% of males | Red/Green elements remapped to Blue/Yellow |
| Tritanopia | Blue-yellow | ~0.01% | Blue/Yellow elements remapped to Red/Cyan |

### Color Remapping Table

| Game Element | Default Color | Deuteranopia | Protanopia | Tritanopia |
|-------------|--------------|-------------|-----------|-----------|
| Health (HP) | Infection Cyan | Unchanged | Unchanged | Unchanged |
| Damage (enemy) | Scorch Red | Orange (#FF8C00) | Yellow (#FFD700) | Red (unchanged) |
| Healing | Bloom Green | Blue (#4A90D9) | Blue (#4A90D9) | Cyan (#00CED1) |
| Poison/Acid | Corrode Orange | Unchanged | Unchanged | Magenta (#FF00FF) |
| Infection glow | Infection Cyan | Unchanged | Unchanged | Unchanged |
| Enemy telegraph | Scorch Red + flash | Orange + flash + shape | Yellow + flash + shape | Red + shape |

### Shape Reinforcement

All gameplay-critical color information is also conveyed via shape or pattern:

| Information | Color | Shape/Pattern Backup |
|------------|-------|---------------------|
| Damage source | Red | Exclamation mark icon overlay |
| Healing source | Green | Plus sign icon overlay |
| Poison zone | Orange | Skull pattern on ground tiles |
| Safe zone | Green | Checkmark pattern |
| Enemy telegraph | Red | Directional arrows + screen edge flash |
| Boon rarity | Color border | Border thickness increases with rarity |

## Text Scaling

| Setting | Range | Default | Step |
|---------|-------|---------|------|
| UI text scale | 75% - 200% | 100% | 25% increments |
| Subtitle text scale | 75% - 250% | 100% | 25% increments |
| Damage number scale | 50% - 200% | 100% | 25% increments |

### Scaling Rules

| Rule | Detail |
|------|--------|
| Minimum readable size | No text below 10px at any scale on any supported resolution |
| Container scaling | Text containers expand to fit scaled text (no clipping) |
| Line wrapping | Enabled for all text at all scales |
| Icon scaling | Icons scale proportionally with text (maintain icon-to-text ratio) |
| HUD reflow | At 150%+, HUD elements reposition to avoid overlap |

## Input Remapping Screen

### Layout

```
+----------------------------------------------+
| INPUT REMAPPING                                |
|                                                |
| Category: [Combat] [Movement] [UI] [Social]   |
|                                                |
| Action          Keyboard    Controller         |
| ---------       --------    ----------         |
| Light Attack    LMB         X / Square         |
| Heavy Attack    RMB         Y / Triangle       |
| Jump            Space       A / Cross          |
| Dash            Shift       B / Circle         |
| Function 1      Q           RB + X             |
| Function 2      E           RB + Y             |
| Function 3      R           RB + A             |
| Function 4      F           RB + B             |
| Bind (Heal)     C           LB                 |
| Interact        V           D-Pad Down         |
| ...                                            |
|                                                |
| [RESET TO DEFAULT]              [BACK]         |
+----------------------------------------------+
```

### Remapping Rules

| Rule | Detail |
|------|--------|
| Full remapping | Every action can be rebound to any key/button |
| Conflict detection | Warning if binding conflicts with another action |
| Multi-bind | Same key can be bound to two actions if they are in different contexts |
| Modifier keys | Ctrl, Alt, Shift can be used as modifiers (e.g., Shift+Q) |
| Controller + keyboard | Both can be active simultaneously |
| Preset profiles | 3 saveable profiles + Default |
| One-handed mode | Preset that maps all actions to one side of keyboard + mouse |

## Subtitle Options

| Setting | Options | Default |
|---------|---------|---------|
| Subtitles enabled | On / Off | On |
| Subtitle size | Small / Medium / Large / Extra Large | Medium |
| Background opacity | 0% / 25% / 50% / 75% / 100% | 75% |
| Speaker label | On / Off | On |
| Word-by-word highlight | On / Off | On |
| Subtitle position | Bottom / Top | Bottom |
| Font | Default / Dyslexia-friendly (OpenDyslexic) | Default |

## Screen Reader Support

| Feature | Implementation |
|---------|---------------|
| UI navigation announce | All focused UI elements announce name + role via TTS |
| Menu structure | Headings and groups exposed to screen reader hierarchy |
| Button state | Disabled/enabled/selected state announced |
| Progress bars | Percentage announced on focus and on change |
| Notifications | Toast text sent to screen reader queue |
| Combat readout | Optional audio description of combat state every 5s |
| Platform TTS | Windows Narrator / macOS VoiceOver integration via native APIs |

### Screen Reader Priority

| Priority | Content |
|----------|---------|
| 1 (immediate) | HP critical warning, death, boss phase change |
| 2 (high) | Damage received, boon selection options |
| 3 (medium) | Navigation, menu items, notifications |
| 4 (low) | Ambient information, stats display |

## Additional Accessibility Options

| Feature | Setting | Default |
|---------|---------|---------|
| Screen shake | Off / Reduced / Full | Full |
| Flashing effects | Off / Reduced / Full | Full |
| Hold vs Toggle | Toggle option for all hold-to-activate inputs | Hold |
| Auto-aim assist | Off / Light / Strong | Off |
| Audio descriptions | Off / Combat / Full | Off |
| High contrast mode | Off / On (increases all contrast ratios to 7:1+) | Off |
| Motion reduction | Off / On (reduces parallax, particle count, camera movement) | Off |
| Button mashing | Replace with hold (QTE replacement) | Off |
| Game speed | 100% / 80% / 60% (solo only) | 100% |

## Accessibility Menu Position

Accessibility settings are accessible from:
1. First boot wizard (before main menu)
2. Settings > Accessibility (always)
3. Pause menu > Settings > Accessibility (in-game)

First boot presents a simplified accessibility wizard with the most impactful options: colorblind mode, text scale, subtitle settings, and input scheme.

## Implementation Notes

- Colorblind mode applied via `ColorblindFilter` URP Renderer Feature (post-process color matrix)
- Text scaling uses `AccessibilityScaler` component that modifies TextMeshPro font size at runtime
- Input remapping uses Unity Input System rebinding API with `InputRemapManager` persistence layer
- Screen reader integration via `ScreenReaderBridge` using platform-native TTS (Windows: SAPI, macOS: NSSpeechSynthesizer)
- Accessibility settings stored in `AccessibilityConfig` ScriptableObject, serialized to player prefs
- High contrast mode overrides URP Volume profile with enhanced contrast color grading
- Game speed modifier applied via `Time.timeScale` (solo only, disabled in multiplayer)
- All accessibility features tested with automated UI tests validating contrast ratios and focus order
