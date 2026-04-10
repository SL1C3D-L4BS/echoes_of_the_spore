# Codex Entries

## Overview

The Codex is the in-game encyclopedia. It contains 300+ entries across 5 categories, unlocked through gameplay actions. Codex entries are the tertiary storytelling channel -- they confirm and expand on what the player has seen through murals and environmental storytelling.

## Categories

| Category | Entry Count | Unlock Method |
|---|---|---|
| Bestiary | 80 | Kill enemy type + use all 3 Function modes against it |
| Civilizations | 70 | Discover Fossil Murals + zone exploration |
| Flora & Fungi | 50 | Interact with environmental objects, harvest nodes |
| Mycelium Studies | 60 | Infection milestones, Fusion events, Spore Plan use |
| Echoes (Meta) | 40+ | Rare events, True Ending, cross-system achievements |
| **Total** | **300+** | -- |

## Unlock Conditions by Category

### Bestiary (80 entries)

| Sub-category | Entries | Unlock Condition |
|---|---|---|
| Tier 1 types | 12 | Kill 10 of the type |
| Tier 1 advanced | 12 | Kill 50 of the type + discover variant |
| Tier 2 types | 12 | Kill 5 of the type using all 3 Function modes |
| Tier 2 advanced | 12 | Kill 20 of the type + exploit weakness |
| Tier 3 types | 8 | Defeat the Apex enemy |
| Mycelium Hunters | 4 | Survive 60s of Hunter chase / Attune to a Hunter |
| Zone Bosses | 8 | Defeat each zone boss |
| Boss Lore | 8 | Defeat boss + find related Fossil Mural |
| Ecosystem | 4 | Witness Tier 2 hunting Tier 1 / Tier 3 hunting Tier 2 |

### Civilizations (70 entries)

| Sub-category | Entries | Unlock Condition |
|---|---|---|
| Per civilization (x7) | 8 each (56) | Discover zone murals (progressive unlock per mural) |
| Progenitor | 8 | Zone 8 murals + True Ending participation |
| Cross-civilization | 6 | Discover murals from 2+ zones that share a symbol/theme |

### Flora & Fungi (50 entries)

| Sub-category | Entries | Unlock Condition |
|---|---|---|
| Fungal nodes (per zone) | 16 | Harvest 5 nodes in a zone |
| Environmental hazards | 12 | Take damage from each hazard type |
| Crafting materials | 15 | Collect each material type |
| Rare specimens | 7 | Discover hidden environmental objects (1 per zone + hub) |

### Mycelium Studies (60 entries)

| Sub-category | Entries | Unlock Condition |
|---|---|---|
| Infection stages | 12 | Reach each Infection milestone (10, 25, 40... 95) |
| Fusion mechanics | 10 | Perform Fusion events (first fuse, 4P fuse, boss fuse, etc.) |
| Spore Plan | 8 | Execute Spore Plans with various compositions |
| Mycelium Voice | 15 | Hear specific Voice trigger categories |
| Community Tree | 10 | Unlock Community Tree branches |
| Rite of Echoes | 5 | Participate in Rite events (win, lose, spectate) |

### Echoes -- Meta (40+ entries)

| Sub-category | Entries | Unlock Condition |
|---|---|---|
| True Ending | 6 | Participate in the vote, experience outcomes |
| World state changes | 8 | Observe post-vote world transformations |
| Developer commentary | 10 | Find hidden developer markers (audio logs, meta-referential) |
| Cross-run discoveries | 8 | Events that span multiple delves |
| Community milestones | 8+ | Server-wide achievements (evolving, added post-launch) |

## Entry Structure

Each Codex entry follows a standard template:

```
Title: [Entry Name]
Category: [Category/Sub-category]
Unlock: [Condition description]
Rarity: [Common | Uncommon | Rare | Legendary]

[Body text: 50-150 words. Written in-universe as observations/studies.
 Tone: clinical for Bestiary, reverent for Civilizations, curious for Flora,
 intimate for Mycelium Studies, reflective for Echoes.]

[Optional: Cross-reference links to related entries]
[Optional: Attached illustration (mural fragment or creature sketch)]
```

## Reward Distribution

| Milestone | Entries Unlocked | Reward |
|---|---|---|
| 25 entries | Any 25 | Title: "Curious" |
| 50 entries | Any 50 | Codex UI theme: Ancient Parchment |
| 100 entries | Any 100 | Cosmetic: Scholar's Lantern (back attachment) |
| 150 entries | Any 150 | Lore Depth +5 (toward True Ending) |
| 200 entries | Any 200 | Title: "Archivist" |
| 250 entries | Any 250 | Cosmetic: Codex Armor set (visible book pages) |
| 300 entries | All entries | Title: "Memory Complete" + unique Canopy trophy |

### Category Completion Rewards

| Category | Entries Required | Reward |
|---|---|---|
| Bestiary (all) | 80 | Enemy HP bars show exact mask count |
| Civilizations (all) | 70 | Ghost echoes of civilization NPCs appear in zones |
| Flora & Fungi (all) | 50 | Fungal nodes drop double resources |
| Mycelium Studies (all) | 60 | Mycelium Voice speaks player's name (custom VO, stretch goal) |
| Echoes (all) | 40 | Credits sequence with developer commentary |

## Rarity Distribution

| Rarity | Count | Percentage | Typical Unlock |
|---|---|---|---|
| Common | 150 | 50% | Standard gameplay actions |
| Uncommon | 90 | 30% | Requires specific conditions or exploration |
| Rare | 45 | 15% | Hidden discoveries, rare events |
| Legendary | 15 | 5% | Cross-system achievements, True Ending, exhaustive collection |

## Codex UI

| Element | Description |
|---|---|
| Navigation | Tab bar for 5 categories. Grid view within each. |
| Entry card | Thumbnail + title + rarity border color + lock/unlock status |
| Locked entries | Silhouette thumbnail. Title hidden. Category visible. Hint text for unlock condition. |
| Read indicator | Unread entries glow. Read entries are dimmed. Counter in tab header. |
| Search | Full-text search across unlocked entry bodies |
| Bookmarks | Player can bookmark up to 20 entries for quick access |

## Implementation Notes

- Codex data defined in `CodexDatabase` ScriptableObject (one per category)
- Each entry: `CodexEntry` struct with id, title, body, rarity, unlock_condition_id, cross_refs, sprite
- Unlock tracking: `CodexProgressComponent` on player entity, persisted in save data
- Unlock events fire through `EventBus<CodexUnlocked>` for UI notification and achievement tracking
- Body text is localized via string table keys: `codex_{category}_{entry_id}_body`
- Illustrations are Addressable sprites loaded on-demand when entry is opened
- Codex accessible from pause menu and Canopy hub terminal
