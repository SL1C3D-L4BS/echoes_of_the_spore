# Fossil Murals -- Environmental Lore System

## Overview

Fossil Murals are wall-embedded artworks scattered throughout the world. They are the primary vehicle for deep lore delivery, depicting the history of the seven civilizations that preceded the player. Each mural is revealed through a custom SDF shader that simulates excavation.

## Distribution

| Zone | Mural Count | Themes |
|---|---|---|
| 1: Sporehaven | 3 | Progenitor creation, first spores, the Seeding |
| 2: Cortex | 5 | The Thinkers civilization, neural architecture, the Great Calculation |
| 3: Ferment Pits | 4 | The Brewers civilization, fermentation rituals, chemical ascension |
| 4: Chitinhall | 5 | The Builders civilization, hive construction, structural collapse |
| 5: Rot Garden | 4 | The Cultivators civilization, symbiotic farming, the Blight |
| 6: Myco-Forge | 4 | The Smiths civilization, fungal metallurgy, the Overforge |
| 7: Necroveil | 5 | The Seekers civilization, death research, veil experiments |
| 8: Necrospore Core | 5 | The Progenitor's truth, Mycelium origin, endgame revelations |
| Hub: Canopy | 5 | Cross-civilization connections, prophecy, the player's role |
| **Total** | **40** | -- |

## SDF Reveal Shader

### Mechanic

1. Player approaches mural location (within 3 units)
2. Interaction prompt appears ("Examine")
3. On interaction, player enters reveal mode (camera zooms to mural surface)
4. Player "brushes" across the surface using aim input
5. SDF distance field controls opacity: revealed regions fade from stone to painted surface
6. Brush radius: 0.5 units. Reveal is permanent (saved per character).
7. Full reveal at 85% coverage (remaining 15% auto-fills with fade animation)

### Shader Parameters

| Parameter | Value | Description |
|---|---|---|
| _RevealTex | RenderTexture (512x512) | Per-mural reveal state texture |
| _BrushRadius | 0.05 (UV space) | Brush size in UV coordinates |
| _EdgeSoftness | 0.02 | Soft edge on reveal boundary |
| _StoneColor | #8B7355 | Unrevealed surface color |
| _PaintSaturation | 0.0 to 1.0 (animated) | Fade-in of color after reveal |
| _GlowIntensity | 0.3 | Subtle bioluminescent edge glow on revealed areas |

### Reveal State Persistence

- Reveal texture serialized as compressed byte array per mural ID
- Stored in character save data
- Multiplayer: each player has independent reveal state. Revealing is personal.

## Mural Catalog

### Zone 1: Sporehaven (3 murals)

| ID | Title | Content | Discovery Condition |
|---|---|---|---|
| M-01-01 | "The First Breath" | Progenitor exhaling spores into void. Stars forming from spore clusters. | Main path, room 3 |
| M-01-02 | "Roots Below" | Vast root network spreading beneath the world. Seven branches, seven futures. | Hidden room behind breakable wall |
| M-01-03 | "The Seeding" | Seven seeds planted in seven soils. Each seed glows a different color. | After defeating Spore Mother |

### Zone 2: Cortex (5 murals)

| ID | Title | Content | Discovery Condition |
|---|---|---|---|
| M-02-01 | "The Great Calculation" | Thinkers arranged in neural patterns, foreheads connected by tendrils. | Main path, room 5 |
| M-02-02 | "The Answer" | A single glyph surrounded by chaos. The Thinkers recoiling in horror. | Hidden room, noise puzzle |
| M-02-03 | "Neural Decay" | Network dissolving. Individual Thinkers disconnecting, faces blank. | Post-boss, behind Architect arena |
| M-02-04 | "The Schism" | Two groups of Thinkers facing each other. One reaching up, one reaching down. | Requires Infection 25+ to see (UV-reactive paint) |
| M-02-05 | "Last Thought" | Single Thinker alone, merged with wall, expression peaceful. | Hidden, requires Codex entry M-02-01 through M-02-04 first |

### Zone 7: Necroveil (5 murals)

| ID | Title | Content | Discovery Condition |
|---|---|---|---|
| M-07-01 | "The Veil Experiment" | Seekers peering through a membrane between worlds. Shapes on the other side. | Main path, must avoid Hunters to reach |
| M-07-02 | "What Came Through" | Tendrils reaching from the other side. Seekers embracing them. | Hidden alcove on Hunter patrol route (timing puzzle) |
| M-07-03 | "The Price of Seeing" | Seekers with empty eye sockets, smiling. Vision transferred to the tendrils. | Requires Infection 50+ |
| M-07-04 | "Memory of Light" | A world before the veil. Sunlight. Open sky. Tiny figures below. | After Veil Warden defeat |
| M-07-05 | "The Invitation" | The Mycelium extending a tendril toward the viewer (the player). Breaking the fourth wall. | Requires all Zone 7 murals (M-07-01 through M-07-04) revealed |

### Zone 8: Necrospore Core (5 murals)

| ID | Title | Content | Discovery Condition |
|---|---|---|---|
| M-08-01 | "The Progenitor's Face" | First clear depiction. Not a creature -- a system. A network that became aware. | First room of Zone 8 |
| M-08-02 | "Seven Failures" | All seven civilizations shown in sequence, each smaller than the last. Pattern of consumption. | After AI Director's first spike room |
| M-08-03 | "The Eighth Seed" | An eighth branch on the root network (M-01-02 callback). The seed is the player. | Hidden, requires 30+ total murals revealed |
| M-08-04 | "FEED" | The Mycelium spreading across worlds. Ecstatic growth. Infinite consumption. | Pre-boss room |
| M-08-05 | "STARVE" | The Mycelium withering. A single spore drifting free. Sadness and hope. | Pre-boss room (opposite wall from M-08-04) |

## Lore Depth Integration

- Each mural discovered adds +1 to the player's Lore Depth counter
- Lore Depth 20 (half of all murals) triggers True Ending eligibility
- Mural discovery also unlocks corresponding Codex entries
- Certain murals require minimum Infection levels to perceive (UV-reactive paint lore justification)

## Rewards

| Milestone | Murals Discovered | Reward |
|---|---|---|
| Novice Archaeologist | 10 | Codex category: Civilizations unlocked |
| Scholar | 20 | Lore Depth 20, True Ending eligible |
| Historian | 30 | Cosmetic: Fossil Frame character border |
| Archivist | 40 (all) | Title: "Memory of the Mycelium" + unique Canopy decoration |

## Implementation Notes

- Mural geometry: flat quads with custom `FossilMuralShader` (URP Shader Graph)
- Reveal texture updated via `CommandBuffer.Blit` with a brush stamp render texture
- Brush input mapped from aim vector to UV space via raycast hit UV coordinates
- Reveal state serialized in `MuralSaveData` struct: mural_id + compressed byte[] of reveal texture
- Discovery events fire through `EventBus<MuralDiscovered>` consumed by Codex, Lore Depth, and achievement systems
- Multiplayer: mural interaction is client-local (no server authority needed). Discovery state synced on session join for Lore Depth calculations.
