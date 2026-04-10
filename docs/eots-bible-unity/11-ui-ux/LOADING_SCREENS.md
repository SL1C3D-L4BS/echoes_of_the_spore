# Loading Screens

## Overview

Loading screens bridge transitions between major game states: boot to hub, hub to delve, zone to zone, and delve to results. They serve three purposes: mask load times, deliver content (tips, lore, stats), and maintain visual atmosphere.

## Loading Screen Types

| Type | Trigger | Min Duration | Max Duration | Content |
|------|---------|-------------|-------------|---------|
| Boot load | Game launch | 3s | 15s | Studio logo, game title |
| Delve load | Matchmaking complete | 2s | 10s | Zone art, tips, party info |
| Zone transition | Zone boundary crossed | 0s (streamed) | 2s (fallback) | Transition corridor (in-game) |
| Return to hub | Delve end / abandon | 1s | 5s | Results preview, lore snippet |
| Reconnect | Connection recovery | 1s | 30s | "Reconnecting..." + tip |

## Delve Loading Screen (Primary)

### Layout

```
+----------------------------------------------+
|                                                |
|  [Zone artwork - full bleed background]        |
|                                                |
|                                                |
|              [Zone Name]                       |
|              [Zone Number]                     |
|                                                |
|                                                |
|                                                |
| +--------------------------------------------+|
| | [Tip icon]  Loading tip text goes here.    ||
| |                                            ||
| | =========================>-----  78%       ||
| | SL1C3D-L4BS                                ||
| +--------------------------------------------+|
+----------------------------------------------+
```

### Elements

| Element | Specification |
|---------|--------------|
| Background art | Random selection from zone art set (3-5 per zone) |
| Zone name | Space Grotesk Bold 36px, Necrospore Gold, centered |
| Zone number | Space Grotesk Regular 18px, Spore White, above zone name |
| Tip area | Bottom 18% of screen, Void Black bar at 80% opacity |
| Tip icon | 24x24, category-matched (gameplay sword, lore scroll, community tree) |
| Tip text | Space Grotesk Regular 16px, Spore White |
| Progress bar | 400x6, Infection Cyan fill, Void Black background, centered in tip area |
| Percentage | Space Mono Regular 12px, right-aligned to progress bar |
| Studio mark | SL1C3D-L4BS wordmark, bottom-left, 80px wide, 60% opacity |

## Procedural Tips

### Tip Categories

| Category | Count | Rotation Weight | Example |
|----------|-------|----------------|---------|
| Gameplay tips | 40 | 40% | "Fusing with a partner doubles your Silk Gauge capacity." |
| Combat tips | 25 | 25% | "All enemy attacks telegraph for at least 250ms. Watch for the flash." |
| Lore snippets | 30 | 20% | "The Rootborn had no word for death. They simply stopped." |
| Community stats | 10+ | 10% | "42,891 spores have fused today across all servers." |
| Seasonal/event | Variable | 5% | Event-specific tips (rotated by server config) |

### Tip Selection Rules

| Rule | Detail |
|------|--------|
| No repeat | Session history tracks shown tips. No repeats until all in category shown. |
| Zone relevance | Tips about current zone weighted 2x during zone loads. |
| New tip boost | Tips added in last 2 patches get 2x weight for 2 weeks. |
| Progression filter | Advanced tips hidden until player has reached relevant content. |
| Lore spoiler filter | Lore snippets filtered by player's Lore Depth (no spoilers). |

### Tip Timing

| Load Duration | Behavior |
|--------------|----------|
| < 2s | Tip displayed for minimum 2s (artificial hold) |
| 2-8s | Single tip displayed for entire load |
| 8-16s | Tip rotates at 8s mark |
| 16s+ | Tip rotates every 8s. Max 4 tips per load. |

## Lore Snippets

Lore snippets are short, atmospheric text fragments that expand on the game world. They are filtered by Lore Depth to prevent spoilers.

### Lore Depth Filtering

| Lore Depth | Available Snippets | Tone |
|-----------|-------------------|------|
| 0-5 | Zone 1-2 atmosphere, basic world | Mysterious, inviting |
| 6-10 | Zone 3-4 civilization hints | Curious, unsettling |
| 11-15 | Zone 5-6 deeper history | Somber, revelatory |
| 16-19 | Zone 7 veil references | Ominous, urgent |
| 20+ | Progenitor truth, post-vote reflections | Intimate, profound |

### Snippet Format

- Max 2 sentences.
- Written in present tense, third person.
- No character names. No mechanical terms.
- Evocative, not expository.

## Art Gallery Rotation

### Zone Art Sets

| Zone | Art Count | Description |
|------|-----------|-------------|
| 1 Sporehaven | 5 | Bioluminescent caverns, root networks, spore clouds |
| 2 Cortex | 4 | Neural pathways, synapse junctions, pulsing walls |
| 3 Ferment Pits | 4 | Acid pools, distillation towers, corroded metal |
| 4 Chitinhall | 5 | Honeycomb halls, collapsed structures, chitin armor |
| 5 Rot Garden | 5 | Terraced gardens, blooming/wilting, the Gardener |
| 6 Myco-Forge | 4 | Forge fires, molten metal, conveyor systems |
| 7 Necroveil | 4 | Impossible geometry, darkness, faint UV glow |
| 8 Core | 3 | Organic chambers, the Heart, veined surfaces |
| Hub | 3 | Canopy overview, Community Tree, peaceful atmosphere |
| **Total** | **37** | -- |

### Art Display Rules

| Rule | Detail |
|------|--------|
| Zone matching | Loading into Zone 3 shows Zone 3 art. Hub shows Hub art. |
| Random selection | Random from zone set. No repeat until all in set shown. |
| Ken Burns effect | Slow zoom (1.02x over 10s) or slow pan (10px/s) for visual interest. |
| Cross-fade | If tip rotates, background stays. Background only changes if art has been shown for 30s+. |

## Community Tree Progress Bar

### Layout

A secondary progress bar showing server-wide Community Tree advancement:

```
Community Tree: Cultivator Branch 67%
[=============================>-----------]
```

| Parameter | Value |
|-----------|-------|
| Position | Above progress bar, left-aligned |
| Size | 400x4, same width as load bar |
| Color | Civilization color of most active branch |
| Visibility | Only shown if player has contributed to the tree |
| Update | Static snapshot taken at load start (not real-time during load) |

## Loading State Communication

| State | Visual Indicator |
|-------|-----------------|
| Loading assets | Progress bar fills smoothly |
| Generating zone | Progress bar pauses at ~30%, text: "The Mycelium grows..." |
| Connecting to server | Progress bar pauses at ~60%, text: "Reaching through the network..." |
| Spawning entities | Progress bar fills from 80-100% |
| Ready | Progress bar full, brief pulse, screen fades to gameplay |

Thematic loading state messages replace generic "Loading..." text.

## Performance

| Metric | Target |
|--------|--------|
| Loading screen init | < 100ms (screen visible before heavy loading begins) |
| Art memory | Max 4MB per background image (compressed) |
| Tip database | < 50KB (text only, loaded at boot) |
| Transition out | 0.5s fade to gameplay |

## Implementation Notes

- Loading screen managed by `LoadingScreenController` in a persistent scene
- Background art loaded as Addressable sprites (`Assets/_Project/Art/LoadingScreens/`)
- Tips loaded from `LoadingTipDatabase` ScriptableObject at boot
- Community stats fetched via `CommunityStatsService` API call on load start
- Progress bar driven by `AsyncOperation.progress` from `SceneManager.LoadSceneAsync`
- Ken Burns effect implemented as DOTween sequence on background RectTransform
- Lore Depth filtering reads `LoreDepthComponent` from player save data
- Minimum display time enforced by `LoadingScreenTimer` coroutine
