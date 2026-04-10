# Account Progression (Permanent)

## Overview

Account progression provides long-term goals that persist across all runs. Nothing earned at the account level is ever lost. These unlocks expand the player's toolkit and cosmetic library without gating core gameplay.

## Echo Functions (32 Total)

Echo Functions are equippable abilities found during delves. Once discovered, they are permanently unlocked and can be equipped at the start of any future run.

### Function Slots

| Slot Type | Count | Unlock Source |
|-----------|-------|--------------|
| Active slots (base) | 3 | Default |
| Active slot 4 | 1 | Community Tree: Evolution Depth 3 |
| Active slot 5 | 1 | Community Tree: Evolution Depth 10 |
| Passive slot | 1 | Community Tree: Evolution Depth 13 |

### Functions by Class (8 per class)

| Class | Function Count | Discovery Method |
|-------|---------------|-----------------|
| Sporeling | 8 | Found in delve rooms (random drop from special enemies) |
| Rootwarden | 8 | Found in delve rooms |
| Bloomcaster | 8 | Found in delve rooms |
| Decayer | 8 | Found in delve rooms |

### Function Discovery Rules

| Rule | Value |
|------|-------|
| Drop chance per special enemy | 5% (first discovery), 0% (already owned) |
| Guaranteed drop zones | Zone 4 and Zone 8 bosses each drop 1 undiscovered Function |
| Discovery at Heat 10+ | Drop chance +2% per 10 Heat |
| Cross-class Functions | Cannot equip Functions from other classes |
| Function preview | Silhouette shown before pickup; accept or pass |

### Function Categories

| Category | Count per Class | Behavior |
|----------|----------------|----------|
| Offensive | 3 | New attack or damage modifier |
| Defensive | 2 | Shield, dodge enhancement, damage reduction |
| Mobility | 1 | Movement ability (teleport, grapple, etc.) |
| Utility | 1 | Resource/information advantage |
| Ultimate | 1 | High-cost, high-impact (requires full Silk + cooldown) |

## Crests (12 Total)

Crests modify the player's base stats and passive behavior. One equipped per run (two with Community Tree: Evolution Depth 15).

| # | Crest Name | Source | Effect |
|---|-----------|--------|--------|
| 1 | Crest of the Sporeling | Default | Balanced stats (1.0x all) |
| 2 | Crest of the Lithic | Zone 1 Boss kill | +10% damage, Petrify on heavy hit (5% chance) |
| 3 | Crest of the Luminari | Zone 2 Boss kill | +15% crit chance, Daze on crit |
| 4 | Crest of the Verdant | Zone 3 Boss kill | +20% Silk fill rate, Bloom on kill |
| 5 | Crest of the Ferrox | Zone 4 Boss kill | +10% defense, Corrode on dash attack |
| 6 | Crest of the Glacine | Zone 5 Boss kill | +1 Shatter stack per hit, Shatter threshold -1 |
| 7 | Crest of the Pyreth | Zone 6 Boss kill | +15% attack speed, Scorch on light chain finisher |
| 8 | Crest of the Umbral | Zone 7 Boss kill | +25% backstab damage, Void Mark on dodge |
| 9 | Crest of the Resonant | Zone 8 Boss kill | Harmonic counter decay -50% |
| 10 | Crest of Fusion | 50 Fusions completed | Fusion merge time -50%, shared Silk +20% |
| 11 | Crest of the Rot | Heat 30 clear | Infection gain -25%, all tier thresholds +10 |
| 12 | Crest of the Archivist | 200 Codex entries | Echo drops +25%, Boon reroll free |

## Account-Wide Statistics

Tracked permanently, visible in player profile:

| Stat Category | Tracked Metrics |
|--------------|-----------------|
| Combat | Total kills, highest combo, total damage dealt, total damage taken |
| Survival | Total overloads, total true deaths, longest deathless streak (rooms) |
| Progression | Total Echoes earned (lifetime), highest Heat cleared per class |
| Cooperation | Total Fusions, total Spore Plans executed, co-op hours |
| Infection | Peak Infection reached, total purification cost spent, Tier 5 runs |
| Collection | Functions discovered (X/32), Crests earned (X/12), Codex (X/300+) |
| Time | Total playtime, fastest full clear per Heat level |

## Prestige System

| Threshold | Reward |
|-----------|--------|
| All 32 Functions discovered | Title: "Echo Collector" |
| All 12 Crests earned | Title: "Crest Bearer" |
| Heat 50 clear (any class) | Title: "Rot Sovereign" |
| Heat 50 clear (all 4 classes) | Title: "Mycelial Architect" + animated border |
| 300+ Codex entries | Title: "Echo Archivist" + Canopy monument |
| 1000 hours playtime | Title: "Eternal Spore" |

## Account Progression Curve

| Milestone | Expected Time | Content |
|-----------|--------------|---------|
| First clear (Heat 0) | 10-20 hours | Core loop understood |
| All Crests (1-9) | 30-50 hours | Boss progression complete |
| 16/32 Functions | 40-60 hours | Half toolkit discovered |
| Heat 20 clear | 50-80 hours | Moderate challenge mastery |
| All 32 Functions | 80-120 hours | Full toolkit |
| Crest 10-12 | 100-150 hours | Endgame milestones |
| Heat 50 clear | 150-300 hours | Mastery peak |
| Full Codex | 200-400+ hours | Completionist goal |

## Data Persistence

```
Supabase: player_account table
  - player_id (uuid)
  - functions_unlocked (int[] — 32 slots, 0/1)
  - crests_unlocked (int[] — 12 slots, 0/1)
  - equipped_functions (int[] — up to 6 slots)
  - equipped_crests (int[] — up to 2 slots)
  - stats (jsonb — all lifetime stats)
  - codex_progress (jsonb — 300+ entry IDs)
  - cosmetics_owned (jsonb — cosmetic IDs)
```
