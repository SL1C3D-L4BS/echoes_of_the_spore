# Accessibility Specification

## Target: WCAG 2.1 AA Equivalent for Games

### Visual Accessibility

| Feature | Implementation | Priority |
|---------|---------------|----------|
| **Colorblind modes** | 3 presets (Protanopia, Deuteranopia, Tritanopia) + custom | P0 |
| **High contrast mode** | Increased outline thickness, brighter UI elements | P1 |
| **Text scaling** | 75% to 200% for all UI text | P0 |
| **Screen shake toggle** | On/Off/Reduced intensity | P0 |
| **Flash reduction** | Limit flash frequency, reduce intensity | P0 |
| **HUD scaling** | 50% to 150% for all HUD elements | P1 |
| **Damage number visibility** | Size, duration, outline options | P2 |
| **Enemy outline** | Optional colored outline on all enemies | P1 |

### Motor Accessibility

| Feature | Implementation | Priority |
|---------|---------------|----------|
| **Full remapping** | All actions remappable (controller + keyboard) | P0 |
| **One-handed mode** | All actions accessible with one hand (configurable) | P1 |
| **Hold vs toggle** | All hold actions can be set to toggle | P0 |
| **Auto-aim assist** | Adjustable aim magnetism (0-100%) | P1 |
| **Dash assist** | Automatic dash on incoming damage (optional) | P2 |
| **Input timing** | Coyote time and jump buffer adjustable (2-12 frames) | P1 |
| **Controller deadzone** | Adjustable per-stick (5-30%) | P0 |
| **Turbo/rapid fire** | Optional for light attack | P2 |

### Auditory Accessibility

| Feature | Implementation | Priority |
|---------|---------------|----------|
| **Subtitles** | All narrator lines, speaker identification, background toggle | P0 |
| **Subtitle customization** | Size, background opacity, color, position | P1 |
| **Visual audio cues** | On-screen indicators for directional sounds | P1 |
| **Mono audio** | Stereo → mono downmix option | P0 |
| **Volume per bus** | Master, Music, SFX, Voice, Narrator (independent) | P0 |
| **Spore-Talk text** | Speech-to-text option for VOIP (accessibility only) | P2 |

### Cognitive Accessibility

| Feature | Implementation | Priority |
|---------|---------------|----------|
| **Tutorial replay** | Re-access onboarding from settings | P1 |
| **Objective marker** | Optional waypoint to current objective | P1 |
| **Difficulty options** | Beyond Heat: "Assist Mode" (more HP, slower enemies) | P1 |
| **Pause in solo** | Full pause available in single-player | P0 |
| **Minimap options** | Zoom, opacity, always-visible rooms, enemy markers | P1 |
| **Consistent UI** | Same button = same action across all menus | P0 |

## Assist Mode (Separate from Heat)

Adjustable parameters that do NOT affect Echo earnings or Community Tree:

| Parameter | Default | Assist Range |
|-----------|---------|-------------|
| Player HP multiplier | 1.0x | 1.0x - 3.0x |
| Enemy damage multiplier | 1.0x | 0.25x - 1.0x |
| Enemy speed multiplier | 1.0x | 0.5x - 1.0x |
| I-frame duration | 20 frames | 20 - 40 frames |
| Silk Gauge fill rate | 5% per hit | 5% - 20% |

**Rule:** Assist Mode runs earn 0 Echoes and don't count for Codex/leaderboards. No shame — clearly labeled as practice/accessibility, not "easy mode."

## Platform-Specific

| Platform | Additional Requirements |
|----------|----------------------|
| PS5 | DualSense haptic presets (none/light/full), Activity Cards |
| Xbox | Copilot support, Xbox Accessibility Guidelines |
| Switch | Touch screen support for menus, gyro aim option |
| PC | Mouse+keyboard full support, ultrawide (21:9) |
