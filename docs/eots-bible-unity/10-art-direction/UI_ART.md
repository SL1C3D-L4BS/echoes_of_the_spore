# UI Art

## Overview

All UI in Echoes of the Spore follows the "Brutalist Bioluminescence" aesthetic applied to interface design: dark, geometric frames with organic accent elements. UI must be readable at 720p, scale cleanly to 4K, and support three colorblind modes.

## Menu Style Guide

### Visual Language

| Element | Style |
|---------|-------|
| Backgrounds | Void Black (#0A0A0F) with subtle noise texture (2% grain) |
| Panels | Mycelium Charcoal (#1A1A2E) with 1px Spore White (#E8E4D9) border |
| Cards (boons, items) | Mycelium Charcoal fill, 2px border colored by rarity or civilization |
| Dividers | 1px Spore White at 30% opacity, horizontal only |
| Corners | Sharp 90-degree angles. No rounded corners except health masks. |
| Shadows | Drop shadow on modals: Void Black, 8px offset, 50% opacity |

### Rarity Border Colors

| Rarity | Border Color | Glow |
|--------|-------------|------|
| Common | Spore White (#E8E4D9) | None |
| Uncommon | Bloom Green (#2ECC71) | Faint (2px, 20% opacity) |
| Rare | Glacine Blue (#3498DB) | Medium (3px, 40% opacity) |
| Epic | Void Purple (#6C3483) | Strong (4px, 60% opacity) |
| Legendary | Necrospore Gold (#C9A84C) | Intense (5px, 80% opacity, pulsing) |

## Icon Grid

### Specifications

| Parameter | Value |
|-----------|-------|
| Grid size | 32x32 pixels (base resolution at 1080p) |
| Padding | 2px internal padding (28x28 usable area) |
| Line weight | 2px stroke, Spore White on transparent background |
| Fill | Flat color fill using palette colors only |
| Silhouette test | Recognizable as a 16x16 silhouette |
| Export format | PNG with alpha, 1x (32px), 2x (64px), 4x (128px) variants |

### Icon Categories

| Category | Count (est.) | Style Notes |
|----------|-------------|-------------|
| Function abilities | 48 (12 classes x 4) | Action-oriented, dynamic poses |
| Boon icons | 70+ | Civilization-themed, organic shapes |
| Status effects | 14 | Simple geometric + color coded |
| HUD elements | 20 | HP mask, silk gauge, infection meter |
| Menu navigation | 15 | Standard (settings gear, back arrow, etc.) |
| Item/material | 30 | Recognizable silhouette, flat color fill |
| Achievement badges | 25 | Detailed, unique per achievement |

## Font Usage

### Primary Fonts

| Font | Weight | Usage | Minimum Size (1080p) |
|------|--------|-------|---------------------|
| Space Grotesk | Bold | Headlines, boss titles, menu headers | 28px |
| Space Grotesk | Medium | Body text, descriptions, tooltips | 16px |
| Space Grotesk | Regular | Secondary text, labels | 14px |
| Space Mono | Regular | Damage numbers, stats, timers, code-like text | 14px |

### Typography Rules

| Rule | Detail |
|------|--------|
| Text color | Spore White (#E8E4D9) for body. Necrospore Gold for emphasis. |
| Line height | 1.4x font size |
| Letter spacing | 0 for body, +2% for headlines, +5% for all-caps labels |
| All-caps | Used only for: boss title cards, section headers, button labels |
| Number display | Tabular figures (monospace numbers) via Space Mono for all numeric data |
| Max line length | 60 characters per line in any text container |

## Button States

### Standard Button

| State | Background | Border | Text | Effect |
|-------|-----------|--------|------|--------|
| Default | Mycelium Charcoal | 1px Spore White | Spore White | None |
| Hover | Mycelium Charcoal + 10% lightened | 2px Infection Cyan | Spore White | Subtle glow on border |
| Pressed | Void Black | 2px Infection Cyan | Infection Cyan | Scale 0.95x, instant |
| Disabled | Void Black, 50% opacity | 1px Spore White, 30% | Spore White, 40% | None |
| Selected | Mycelium Charcoal | 2px Necrospore Gold | Necrospore Gold | Steady glow |

### Destructive Button (quit, abandon, discard)

| State | Background | Border | Text |
|-------|-----------|--------|------|
| Default | Transparent | 1px Scorch Red | Scorch Red |
| Hover | Scorch Red 20% fill | 2px Scorch Red | Spore White |
| Pressed | Scorch Red 40% fill | 2px Scorch Red | Spore White |

### Controller/Keyboard Navigation

| Parameter | Value |
|-----------|-------|
| Focus indicator | 2px animated border (Infection Cyan), dashed pattern scrolling |
| Focus audio | Subtle click (50ms, -20dB) |
| Selection audio | Confirm tone (100ms, -15dB) |
| Navigation wrap | Enabled (past last item wraps to first) |

## Modal Overlays

### Specifications

| Parameter | Value |
|-----------|-------|
| Backdrop | Void Black at 70% opacity, full screen |
| Modal panel | Mycelium Charcoal, 2px Spore White border |
| Max width | 600px at 1080p (scales with resolution) |
| Animation | Fade in 0.2s (backdrop), scale 0.95 to 1.0 + fade 0.15s (panel) |
| Dismiss | Escape key, B button, click outside modal |
| Stack limit | Max 2 modals stacked (confirmation on top of settings, etc.) |

### Common Modals

| Modal | Trigger | Content |
|-------|---------|---------|
| Quit confirmation | Quit button during delve | "Abandon this delve? Progress will be lost." |
| Boon detail | Hover/select boon card for 1s | Full boon description, stats, civilization lore snippet |
| Vote confirmation | Walking into FEED/STARVE portal | "Cast your vote? You may change it within 48 hours." |
| Matchmaking cancel | Back button during queue | "Leave matchmaking queue?" |

## Responsive Scaling

| Resolution | Scale Factor | Min Text Size | Icon Size |
|-----------|-------------|---------------|-----------|
| 720p | 0.67x | 10px | 22x22 |
| 1080p | 1.0x (reference) | 14px | 32x32 |
| 1440p | 1.33x | 18px | 42x42 |
| 4K | 2.0x | 28px | 64x64 |

All UI uses Unity Canvas Scaler in "Scale With Screen Size" mode, reference resolution 1920x1080.

## Implementation Notes

- UI prefabs stored in `Assets/_Project/UI/Prefabs/` organized by screen
- Font assets: Space Grotesk and Space Mono imported as TextMeshPro font assets with SDF rendering
- Icon sprites packed into dedicated UI Sprite Atlas (`Assets/_Project/Art/Atlases/UI/`)
- Button state transitions handled by custom `EotsButton` component extending Unity Selectable
- Modal system managed by `ModalController` singleton with stack-based push/pop
- All UI text uses localized string table keys via Unity Localization package
- Canvas render mode: Screen Space - Overlay for HUD, Screen Space - Camera for menus
