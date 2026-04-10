# 11 — UI/UX, INPUT & READABILITY

## HUD Layout (In-Delve)

### Minimal HUD (Default)
- **Top-left:** HP masks (Hollow Knight style, 5-12 mask icons)
- **Top-left below HP:** Silk Gauge (horizontal bar, fills cyan)
- **Top-right:** Infection Level (numerical 0-100 + tier indicator icon)
- **Bottom-left:** Active Functions (4 icons with cooldown radials)
- **Bottom-center:** Combo counter (appears on hit, fades after 500ms gap)
- **Bottom-right:** Minimap (toggleable, shows room connections, current position, fog of war)
- **Center-bottom:** Interaction prompt (contextual, e.g., "Hold E to Fuse")

### Fusion HUD (When Fused)
- **Top-left:** Combined HP bar (larger, shows individual partner contributions as segments)
- **Top-center:** Spore Gauge (fills for Spore Plan access)
- **Bottom:** All partners' Function icons visible, labeled by role (Pilot/Gunner/Support/Relay)
- **Role indicator:** Small icon next to each player's name showing their current role

### Information Hierarchy
1. HP + Silk (survival — always visible)
2. Active cooldowns (tactical — always visible)
3. Infection level (strategic — always visible)
4. Combo counter (feedback — transient)
5. Minimap (navigation — toggleable)
6. Damage numbers (feedback — transient, optional in settings)

## Input Mapping

### Keyboard + Mouse (Default PC)
| Action | Binding | Notes |
|---|---|---|
| Move | WASD | |
| Jump | Space | Hold for variable height |
| Dash | Shift | I-frames, directional |
| Light Attack | Left Click | |
| Heavy Attack | Right Click | Hold to charge |
| Function 1-4 | Q, E, R, F | Bound to Active slots |
| Bind (Heal) | C | Costs full Silk Gauge |
| Interact | V | Doors, NPCs, Fusion Nodes |
| Fusion Request | T | Toggle (request/separate) |
| Spore Plan | G | Only available when Fused + Gauge full |
| Map Toggle | Tab | |
| Spore-Talk (PTT) | Caps Lock | Push-to-talk |

### Controller (Gamepad)
| Action | Binding |
|---|---|
| Move | Left Stick |
| Jump | A / X (PS) |
| Dash | B / Circle |
| Light Attack | X / Square |
| Heavy Attack | Y / Triangle |
| Functions | RB+Face buttons |
| Bind (Heal) | LB |
| Interact | D-Pad Down |
| Fusion | D-Pad Up |
| Spore Plan | LT + RT simultaneous |
| Map | Select / Touchpad |
| PTT | D-Pad Right |

## Menu Flow
```
Main Menu → Play → Select Class → Select Crest → Loadout (Functions) → Matchmake → Delve
                → Community Tree (view/contribute)
                → Echo Codex (browse achievements)
                → Wish Board (accept quests)
                → Purification Pool (spend Echoes to reduce Infection)
                → Settings
                    → Video (resolution, FPS cap, colorblind mode)
                    → Audio (master, music, SFX, voice, narrator, Spore-Talk)
                    → Controls (full rebinding, sensitivity)
                    → Accessibility (see Section 22)
                    → Network (region, NAT test)
```

## Readability Standards
- All interactive elements must be distinguishable at 720p resolution
- Enemy telegraph effects must be visible for minimum 15 frames (250ms) before damage
- Critical UI text must meet WCAG AA contrast ratio (4.5:1 minimum)
- Damage numbers use tabular-aligned monospace font, minimum 14px at 1080p
- Colorblind modes: Deuteranopia, Protanopia, Tritanopia (remap all gameplay-critical colors)
