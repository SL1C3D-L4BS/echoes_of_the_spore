# Controller Support

## Overview

Echoes of the Spore supports full controller play on all platforms. Controller and keyboard/mouse can be used simultaneously. The game auto-detects input device and swaps button prompts dynamically. All menus and gameplay are fully navigable with controller.

## Controller Layout

### Standard Gamepad (Xbox / PlayStation)

| Action | Xbox | PlayStation | Context |
|--------|------|------------|---------|
| Move | Left Stick | Left Stick | Always |
| Aim (ranged/ability) | Right Stick | Right Stick | Combat |
| Jump | A | Cross | Always |
| Dash | B | Circle | Always |
| Light Attack | X | Square | Combat |
| Heavy Attack | Y | Triangle | Combat (hold to charge) |
| Function 1 | RB + X | R1 + Square | Combat |
| Function 2 | RB + Y | R1 + Triangle | Combat |
| Function 3 | RB + A | R1 + Cross | Combat |
| Function 4 | RB + B | R1 + Circle | Combat |
| Bind (Heal) | LB | L1 | Combat (requires full Silk) |
| Interact | D-Pad Down | D-Pad Down | Context |
| Fusion Toggle | D-Pad Up | D-Pad Up | Near partner |
| Spore Plan | LT + RT | L2 + R2 | Fused + gauge full |
| Map Toggle | Select / View | Touchpad Press | Always |
| Spore-Talk (PTT) | D-Pad Right | D-Pad Right | Multiplayer |
| Pause | Menu / Start | Options | Always |
| Codex Quick Access | D-Pad Left | D-Pad Left | Hub only |

### Modifier System (RB / R1)

RB acts as a modifier key for Function access:

| Input | Without RB | With RB |
|-------|-----------|---------|
| X / Square | Light Attack | Function 1 |
| Y / Triangle | Heavy Attack | Function 2 |
| A / Cross | Jump | Function 3 |
| B / Circle | Dash | Function 4 |

RB is held, not toggled. Releasing RB returns face buttons to default actions.

## Radial Menus

### Quick Item Radial

Activated by holding D-Pad Down for 0.5s (tap = Interact):

```
        [Item 1]
   [Item 4]  [Item 2]
        [Item 3]
```

| Parameter | Value |
|-----------|-------|
| Activation | Hold D-Pad Down 0.5s |
| Navigation | Right Stick direction |
| Selection | Release D-Pad Down |
| Cancel | Press B / Circle |
| Slots | 4 (consumable items) |
| Time slowdown | Solo: 50% time scale while open. Multiplayer: real-time. |

### Emote Radial

Activated by holding D-Pad Left in gameplay:

| Parameter | Value |
|-----------|-------|
| Activation | Hold D-Pad Left 0.5s |
| Slots | 8 emotes (customizable in settings) |
| Navigation | Right Stick |
| Selection | Release D-Pad Left |

## Haptic Feedback Specification

### Vibration Events

| Event | Motor | Intensity | Duration | Pattern |
|-------|-------|-----------|----------|---------|
| Light attack hit | Right | 0.3 | 50ms | Single pulse |
| Heavy attack hit | Both | 0.6 | 100ms | Single pulse |
| Dash | Left | 0.2 | 80ms | Single pulse |
| Damage received | Both | 0.5 | 150ms | Double pulse |
| Death | Both | 0.8 | 300ms | Fade-out ramp |
| Boss phase transition | Both | 0.4 | 500ms | Slow wave |
| Infection milestone | Right | 0.3 | 200ms | Heartbeat (2 pulses) |
| Fusion merge | Both | 0.5 | 400ms | Rising intensity |
| Fusion separate | Both | 0.3 | 200ms | Falling intensity |
| Boon acquired | Right | 0.2 | 100ms | Single pulse |
| Heal (Bind) | Left | 0.2 | 300ms | Slow wave |
| Environmental hazard | Left | 0.1 | Continuous | Low rumble while in hazard |

### Adaptive Triggers (PlayStation 5)

| Trigger | Effect | Context |
|---------|--------|---------|
| L2 (Block/Aim) | Resistance at 50% pull | While aiming ranged abilities |
| R2 (Heavy confirm) | Vibration feedback at threshold | Heavy attack charge indicator |
| L2 + R2 (Spore Plan) | Both triggers resist equally | Spore Plan activation ritual feel |

### Haptic Settings

| Setting | Options | Default |
|---------|---------|---------|
| Haptic intensity | Off / Low / Medium / High | Medium |
| Adaptive triggers | On / Off | On (PS5 only) |
| Environmental rumble | On / Off | On |

## Button Prompt System

### Auto-Detection

| Detected Device | Prompt Set | Switch Delay |
|----------------|-----------|-------------|
| Xbox controller | Xbox glyphs (A/B/X/Y) | Instant on input |
| PlayStation controller | PlayStation glyphs (Cross/Circle/Square/Triangle) | Instant on input |
| Nintendo controller | Nintendo glyphs (B/A/Y/X) | Instant on input |
| Keyboard + Mouse | Key labels + mouse icons | Instant on input |
| Steam Deck | Xbox glyphs (default) or custom | Per user setting |

### Prompt Display Rules

| Rule | Detail |
|------|--------|
| Swap trigger | Any input from new device type swaps all prompts |
| Debounce | 0.5s debounce on device switch to prevent flickering |
| Persistent | Last used device persists across sessions |
| Manual override | Settings option to lock to specific prompt set |
| Contextual | Only show relevant prompts (combat prompts in combat, menu prompts in menu) |

## Menu Navigation (Controller)

| Action | Input |
|--------|-------|
| Navigate up/down | Left Stick / D-Pad |
| Navigate left/right | Left Stick / D-Pad |
| Confirm | A / Cross |
| Back | B / Circle |
| Tab switch | LB / RB (L1 / R1) |
| Scroll | Right Stick vertical |
| Page up/down | LT / RT (L2 / R2) |

### Focus System

| Parameter | Value |
|-----------|-------|
| Initial focus | First interactive element on screen load |
| Focus wrap | Enabled (past last wraps to first) |
| Focus memory | Cursor position remembered per screen in navigation stack |
| Focus indicator | 2px dashed border, Infection Cyan, animated scroll |
| Dead zone | 0.3 (stick dead zone for menu navigation) |
| Repeat rate | 0.4s initial delay, then 0.15s repeat for held direction |

## Platform-Specific Notes

| Platform | Notes |
|----------|-------|
| PC (Steam) | Steam Input API for controller detection. Supports Steam Controller, generic XInput/DirectInput. |
| PlayStation | DualSense haptics and adaptive triggers via PS5 SDK. DualShock 4 standard rumble only. |
| Xbox | GameInput API. Impulse triggers on Series X/S controllers. |
| Nintendo Switch | Joy-Con HD Rumble via NX SDK. Supports handheld and docked modes. |
| Steam Deck | Treated as PC controller. Trackpads mapped to right stick by default. |

## Implementation Notes

- Controller input processed by Unity Input System with device-specific action maps
- Haptic feedback driven by `HapticFeedbackSystem` consuming gameplay events via `EventBus`
- Button prompts managed by `InputPromptService` that watches `InputSystem.onDeviceChange`
- Prompt glyph sprites stored in `Assets/_Project/UI/InputGlyphs/` with per-platform atlas
- Radial menus implemented as `RadialMenuController` component with right-stick angle detection
- Adaptive trigger effects via platform abstraction layer in `PlatformHapticsService`
- Dead zone and repeat rate configurable in Settings > Controls
