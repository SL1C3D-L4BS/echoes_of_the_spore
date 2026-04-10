# Community Skill Tree

## Structure
5 branches × 20 depths = 100 total nodes. All players on a server contribute Echoes collectively.

## Cost Formula
```
cost(depth) = floor(1000 × 1.8^depth)
```

| Depth | Cost | Cumulative | Expected Timeline |
|-------|------|------------|------------------|
| 1 | 1,800 | 1,800 | Day 1 |
| 2 | 3,240 | 5,040 | Day 2 |
| 3 | 5,832 | 10,872 | Day 3 |
| 5 | 18,896 | 48,498 | Week 1 |
| 10 | 357,047 | 1,233,569 | Month 1 |
| 15 | 6,746,191 | 20,834,573 | Month 3 |
| 20 | 127,482,280 | 388,006,790 | Month 6+ |

## Five Branches

### 1. Expansion (World Growth)
| Depth | Unlock |
|-------|--------|
| 1 | Zone 2 gate opens |
| 3 | Zone 3 gate opens |
| 5 | Shortcut paths between zones |
| 8 | Secret room type frequency +50% |
| 10 | Zone 5-6 gate opens |
| 13 | Zone 7 gate opens |
| 15 | Branching room paths (multiple routes per zone) |
| 18 | Zone 8 gate opens |
| 20 | **Secret Zone 9 unlocked** |

### 2. Evolution (Player Power)
| Depth | Unlock |
|-------|--------|
| 1 | Dash cooldown -10% |
| 3 | 4th Echo Function slot |
| 5 | Crest switching mid-run (at Rest rooms) |
| 8 | Fusion merge time -30% |
| 10 | 5th Echo Function slot |
| 13 | Passive Echo Function always active |
| 15 | Dual Crest (equip 2 Crests simultaneously) |
| 18 | 3-player Fusion enhanced abilities |
| 20 | **Hyper-Fusion (5-player Fusion)** |

### 3. Ecology (World Life)
| Depth | Unlock |
|-------|--------|
| 1 | Fungal Merchant NPC spawns in Rest rooms |
| 3 | New Tier 1 enemy variant per zone |
| 5 | Ally summon Echo Function |
| 8 | New Tier 2 enemy variant per zone |
| 10 | NPC quest giver in Canopy |
| 13 | Faction system (choose civilization allegiance) |
| 15 | New Tier 3 mini-boss per zone |
| 18 | Ecosystem complexity: Tier 2 enemies form packs |
| 20 | **Adaptive World Boss (roams between zones)** |

### 4. Infrastructure (Hub & Economy)
| Depth | Unlock |
|-------|--------|
| 1 | Stash chest in Canopy (store items between runs) |
| 3 | Basic crafting station |
| 5 | Fast-travel between unlocked zones |
| 8 | Advanced crafting (modify boon effects) |
| 10 | Player housing (customizable room in Canopy) |
| 13 | Auction house (trade cosmetics) |
| 15 | Canopy expansion (new districts) |
| 18 | Cross-server messaging |
| 20 | **Cross-server portal (visit other servers)** |

### 5. Lore (Narrative Progression)
| Depth | Unlock |
|-------|--------|
| 1 | Progenitor cutscene #1 |
| 3 | Luminari civilization full codex |
| 5 | Hidden mural locations revealed on map |
| 8 | Ferrox/Verdant civilization full codex |
| 10 | Mycelium Voice gains new dialogue (200 lines) |
| 13 | Glacine/Pyreth civilization full codex |
| 15 | Umbral civilization full codex (partially corrupted) |
| 18 | Progenitor truth revealed |
| 20 | **True Ending vote: FEED or STARVE** |

## Contribution System

- Each run earns Echoes (100-500 base, multiplied by Heat)
- Post-run: player chooses which branch to contribute to
- Spore Pass ($4.99/mo): 2x contribution multiplier
- Spore Pass holders vote weekly on priority branch
- Individual contribution tracked for leaderboard
- Server-wide progress visible in Canopy (physical tree grows)

## Backend Implementation

```
Supabase: skill_tree_nodes table
  - server_id (uuid)
  - branch (enum: expansion/evolution/ecology/infrastructure/lore)
  - depth (int 1-20)
  - current_echoes (bigint)
  - required_echoes (bigint)
  - unlocked (boolean)
  - unlocked_at (timestamp)

Redis: real-time cache
  - tree:{server_id}:{branch}:current → current contribution count
  - tree:{server_id}:{branch}:depth → current unlocked depth
  - Flush to Supabase every 60 seconds
```
