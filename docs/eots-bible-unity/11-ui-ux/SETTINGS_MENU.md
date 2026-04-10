# Settings Menu

## Overview

The Settings menu is accessible from the Main Menu, Canopy Hub, and Pause Menu. Changes apply immediately with live preview where possible. Settings persist across sessions via player preferences file.

## Settings Categories

| Category | Tab Position | Settings Count |
|----------|-------------|---------------|
| Graphics | 1 | 12 |
| Audio | 2 | 8 |
| Controls | 3 | 10 |
| Accessibility | 4 | 12 |
| Gameplay | 5 | 8 |
| Network | 6 | 5 |

## Graphics Settings

| Setting | Options | Default (PC) | Default (Console) |
|---------|---------|-------------|------------------|
| Resolution | System-detected list | Native | Native (locked) |
| Display mode | Fullscreen / Borderless / Windowed | Borderless | Fullscreen (locked) |
| V-Sync | Off / On | On | On (locked) |
| FPS cap | 30 / 60 / 120 / 144 / Unlimited | 60 | 60 (locked) |
| Quality preset | Low / Medium / High / Ultra / Custom | Auto-detected | Platform default |
| Texture quality | Low / Medium / High | High | Platform default |
| Shadow quality | Off / Low / High | High | Platform default |
| Particle density | Low / Medium / High / Ultra | High | Medium |
| Bloom intensity | 0% - 100% | 60% | 60% |
| Screen shake | Off / Reduced / Full | Full | Full |
| Colorblind mode | Off / Deuteranopia / Protanopia / Tritanopia | Off | Off |
| Camera zoom | 7 - 10 units | 8 | 8 |

### Quality Preset Breakdown

| Setting | Low | Medium | High | Ultra |
|---------|-----|--------|------|-------|
| Texture quality | Low | Medium | High | High |
| Shadow quality | Off | Low | High | High |
| Particle density | Low | Medium | High | Ultra |
| Bloom | 30% | 50% | 60% | 80% |
| Parallax layers | 3 | 4 | 5 | 5 |
| Shader complexity | Reduced | Standard | Full | Full + extras |
| Target framerate | 30 | 60 | 60 | 120+ |

## Audio Settings

| Setting | Range | Default | Step |
|---------|-------|---------|------|
| Master volume | 0 - 100% | 80% | 5% |
| Music volume | 0 - 100% | 70% | 5% |
| SFX volume | 0 - 100% | 80% | 5% |
| Voice (Mycelium) volume | 0 - 100% | 90% | 5% |
| Narrator subtitles | On / Off | On | Toggle |
| Spore-Talk volume | 0 - 100% | 70% | 5% |
| Notification sounds | On / Off | On | Toggle |
| Audio output | System Default / Headphones / Speakers | System Default | List |

### Audio Mixer Routing

```
Master Bus
  |-- Music Bus (BGM, ambient)
  |-- SFX Bus (combat, environment, UI)
  |-- Voice Bus (Mycelium Voice, NOT routed through DSP)
  |-- Spore-Talk Bus (player communication, routed through Infection DSP)
  |-- Notification Bus (toast sounds, alerts)
```

## Controls Settings

| Setting | Options | Default |
|---------|---------|---------|
| Input device | Auto-detect / Keyboard+Mouse / Controller | Auto-detect |
| Button prompts | Auto / Xbox / PlayStation / Nintendo / Keyboard | Auto |
| Key bindings | Full remapping (see ACCESSIBILITY_UI.md) | Default layout |
| Controller dead zone | 0.1 - 0.5 | 0.3 |
| Stick sensitivity | 0.5 - 2.0 | 1.0 |
| Mouse sensitivity | 0.5 - 3.0 | 1.0 |
| Aim assist | Off / Light / Strong | Off (PC) / Light (Console) |
| Vibration intensity | Off / Low / Medium / High | Medium |
| Adaptive triggers | On / Off | On (PS5 only) |
| Input buffer window | 1 - 5 frames | 3 frames |

## Accessibility Settings

| Setting | Options | Default |
|---------|---------|---------|
| Colorblind mode | Off / Deuteranopia / Protanopia / Tritanopia | Off |
| UI text scale | 75% - 200% | 100% |
| Subtitle text scale | 75% - 250% | 100% |
| Subtitle background | 0% - 100% opacity | 75% |
| Subtitle font | Default / Dyslexia-friendly | Default |
| Screen reader | Off / On | Off |
| High contrast mode | Off / On | Off |
| Motion reduction | Off / On | Off |
| Flashing effects | Off / Reduced / Full | Full |
| Hold vs Toggle | Hold / Toggle | Hold |
| Game speed (solo) | 60% / 80% / 100% | 100% |
| Combat audio cues | Off / On | Off |

See ACCESSIBILITY_UI.md for detailed specifications.

## Gameplay Settings

| Setting | Options | Default |
|---------|---------|---------|
| Damage numbers | Off / On | On |
| Damage number size | Small / Medium / Large | Medium |
| Minimap | Off / Compact / Full | Full |
| HUD opacity | 50% - 100% | 100% |
| Auto-heal (Bind) | Off / On (auto-heal at 1 HP mask) | Off |
| Combo counter | Off / On | On |
| Boss HP display | Bar / Percentage / Both | Bar |
| Tutorial hints | Off / On | On (first character only) |

## Network Settings

| Setting | Options | Default |
|---------|---------|---------|
| Region | Auto / NA-East / NA-West / EU-West / EU-East / Asia / OCE | Auto |
| NAT test | Button (runs connectivity test) | -- |
| Spore-Talk mode | Push-to-Talk / Open Mic / Off | Push-to-Talk |
| Show player names | Off / Party only / All | All |
| Cross-play | On / Off | On |

### NAT Test Results

| Result | Icon | Description |
|--------|------|-------------|
| Open | Green check | Full connectivity. All features available. |
| Moderate | Yellow triangle | May have issues hosting. Matchmaking adjusted. |
| Strict | Red X | Cannot host. Relay server required. Increased latency. |

## Per-Platform Defaults

| Setting | PC | PlayStation | Xbox | Switch |
|---------|-----|------------|------|--------|
| Resolution | Native | Native | Native | 720p docked / 540p handheld |
| FPS cap | 60 | 60 | 60 | 30 |
| Quality preset | Auto | High | High | Medium |
| V-Sync | On | Locked On | Locked On | Locked On |
| Aim assist | Off | Light | Light | Light |
| Controller prompts | Auto | PlayStation | Xbox | Nintendo |

## Settings Persistence

| Storage | Content |
|---------|---------|
| Player prefs file | All settings as key-value pairs |
| Cloud sync | Settings synced via platform cloud save (Steam Cloud, PS Plus, Xbox) |
| Profile support | 1 settings profile per platform account |
| Reset | "Reset to Default" button per category + global reset |
| Migration | Settings version number for forward-compatible migration between patches |

## Implementation Notes

- Settings UI built with `SettingsMenuController` managing tab navigation and value binding
- Each setting bound to a `SettingDefinition` ScriptableObject with: key, type, range, default, platform overrides
- Live preview: graphics and audio changes apply immediately. Controls saved on confirm.
- Settings serialized to `PlayerPrefs` (Unity) with platform-specific paths
- Cloud sync uses platform SDK wrappers in `CloudSaveService`
- Quality preset changes trigger `QualitySettingsApplier` to batch-update all dependent settings
- NAT test runs via `NATTestService` using STUN protocol, results cached for session
