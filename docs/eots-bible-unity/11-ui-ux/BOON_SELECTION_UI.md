# Boon Selection UI

## Overview

Boon selection appears in Reward rooms after combat completion. Three boon cards are presented. The player must choose one (no skip option). The UI must communicate rarity, civilization affiliation, mechanical effect, and synergies within 5 seconds of reading.

## Layout

```
+----------------------------------------------+
|              CHOOSE YOUR BOON                  |
|                                                |
|  +----------+  +----------+  +----------+     |
|  |  [ICON]  |  |  [ICON]  |  |  [ICON]  |     |
|  |          |  |          |  |          |     |
|  | Spore    |  | Neural   |  | Acid     |     |
|  | Burst    |  | Link     |  | Coating  |     |
|  |          |  |          |  |          |     |
|  | Uncommon |  | Rare     |  | Common   |     |
|  | Rootborn |  | Thinkers |  | Brewers  |     |
|  +----------+  +----------+  +----------+     |
|                                                |
|  [Effect preview text area]                    |
|                                                |
|  Timer: 30s                [Reroll: 1 left]    |
+----------------------------------------------+
```

## Card Specifications

### Card Dimensions

| Parameter | Value |
|-----------|-------|
| Card size | 200x280 at 1080p |
| Spacing | 32px between cards |
| Total width | 664px (3 cards + 2 gaps) |
| Position | Center screen, vertically centered |

### Card Elements (Top to Bottom)

| Element | Size | Content |
|---------|------|---------|
| Civilization icon | 32x32 | Civilization crest in top-left corner |
| Boon icon | 64x64 | Centered, 16px from top |
| Boon name | 18px Bold | Space Grotesk, centered |
| Effect text | 14px Regular | 1-2 lines describing mechanical effect |
| Rarity label | 12px Regular | Bottom of card, colored by rarity |
| Synergy indicator | 16x16 icons | Bottom-right, shows synergizing boons already held |

### Rarity Glow

| Rarity | Border | Glow Effect | Background Tint |
|--------|--------|------------|-----------------|
| Common | 1px Spore White | None | Mycelium Charcoal |
| Uncommon | 2px Bloom Green | 4px soft glow, 20% opacity | Charcoal + 5% green |
| Rare | 2px Glacine Blue | 6px soft glow, 40% opacity | Charcoal + 5% blue |
| Epic | 2px Void Purple | 8px animated glow, 60% opacity | Charcoal + 5% purple |
| Legendary | 3px Necrospore Gold | 10px pulsing glow, 80% opacity | Charcoal + 8% gold |

### Civilization Icon

Each boon card displays the civilization crest of its origin in the top-left corner:

| Civilization | Icon Color | Icon Shape |
|-------------|-----------|------------|
| Rootborn | Bloom Green | Root tendril |
| Thinkers | Void Purple | Neural node |
| Brewers | Corrode Orange | Flask / droplet |
| Builders | Necrospore Gold | Hexagon |
| Cultivators | Bloom Green + White | Flower |
| Smiths | Scorch Red | Anvil |
| Seekers | Void Purple + Cyan | Eye |

## Effect Preview

When a card is focused (hovered or selected via controller):

| Element | Specification |
|---------|--------------|
| Preview area | Below cards, 600x80, Void Black at 80% |
| Effect text | Full mechanical description, Space Grotesk Regular 16px |
| Stat comparison | If upgrading existing boon: shows delta ("+5% damage" in green) |
| Synergy note | If player holds a synergizing boon: "SYNERGY with [Boon Name]" in Necrospore Gold |
| Duo Echo hint | If combining two same-civilization boons: "DUO ECHO available" in Necrospore Gold |

## Controller / Keyboard Navigation

| Input | Action |
|-------|--------|
| Left/Right arrow, D-pad Left/Right | Move focus between cards |
| Enter / A button | Select focused card |
| Tab / Y button | Toggle detail view (expanded card with full lore text) |
| R / X button | Reroll (if available, costs 1 reroll charge) |
| Mouse hover | Focus card under cursor |
| Mouse click | Select card |

### Focus Behavior

| State | Visual |
|-------|--------|
| Unfocused | Default card appearance |
| Focused | Card scales to 1.05x, border brightens, subtle bounce animation (0.2s) |
| Selected | Card flashes white (0.1s), other cards fade out (0.3s), selected card moves to center |

## Selection Animation

| Phase | Duration | Description |
|-------|----------|-------------|
| 1: Flash | 0.1s | Selected card emits white flash |
| 2: Reject | 0.3s | Unselected cards fade and slide off-screen |
| 3: Center | 0.3s | Selected card moves to screen center |
| 4: Absorb | 0.5s | Card dissolves into particles that flow to player character |
| 5: Activate | 0.5s | Boon activation VFX plays on player (see VFX_SPEC.md) |

Total selection animation: 1.7s. Player regains control after Absorb phase.

## Timer and Reroll

| Parameter | Value |
|-----------|-------|
| Selection timer | 30 seconds. If expired, random boon selected. |
| Timer visual | Circular countdown around "CHOOSE YOUR BOON" text |
| Timer warning | At 10s: timer turns Scorch Red, subtle pulse |
| Timer audio | At 5s: ticking sound effect |
| Reroll charges | 1 per delve (base). Additional charges from Community Tree unlocks. |
| Reroll cost | Consumes 1 charge, regenerates 3 new boons from the pool |
| Reroll animation | Cards flip face-down (0.2s), shuffle (0.3s), flip face-up (0.2s) |

## Multiplayer Behavior

| Scenario | Behavior |
|---------|----------|
| Co-op (2-4P) | Each player sees their own boon selection independently |
| Same room | Boon selection overlays gameplay; other players can continue moving |
| All selecting | If all players in reward room, game soft-pauses (enemies freeze) |
| Timeout | Individual timeout applies. Others are not blocked. |

## Implementation Notes

- Boon selection UI prefab: `Assets/_Project/UI/Prefabs/BoonSelection/BoonSelectionPanel.prefab`
- Card data populated from `BoonDatabase` ScriptableObject via `BoonSelectionSystem`
- Rarity glow uses `BoonCardGlow` component with animated material properties
- Selection animation driven by DOTween sequence triggered by `BoonSelectedEvent`
- Timer managed by `BoonSelectionTimerSystem` with `EventBus<BoonTimerExpired>` fallback
- Reroll state tracked per-delve in `DelveStateComponent`
- Boon pool filtering (civilization weighting from Crest) handled by `BoonPoolService`
