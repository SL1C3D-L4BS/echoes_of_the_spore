# Heat System (Pact of the Mycelium)

## Unlock Condition
Complete all 8 zones at Heat 0 (first clear).

## Structure
15 Conditions, each with 1-5 ranks. Each rank = 1 Heat point. Max Heat = 50.

## Echo Multiplier
```
echo_multiplier = 1.0 + (heat × 0.05)
```

| Heat | Multiplier | Echo Gain Example (base 100) |
|------|-----------|-----|
| 0 | 1.0x | 100 |
| 10 | 1.5x | 150 |
| 20 | 2.0x | 200 |
| 30 | 2.5x | 250 |
| 40 | 3.0x | 300 |
| 50 | 3.5x | 350 |

## The 15 Conditions

| # | Condition | Ranks | Effect per Rank |
|---|-----------|-------|----------------|
| 1 | **Thick Membrane** | 5 | Enemies +20% HP per rank |
| 2 | **Aggressive Spores** | 5 | Enemies +15% damage per rank |
| 3 | **Necrotic Air** | 3 | Passive Infection gain +1/room per rank |
| 4 | **Sealed Roots** | 3 | Rest rooms heal 1 less HP per rank |
| 5 | **Withered Boons** | 3 | Boon offerings -1 option per rank (min 1) |
| 6 | **Festering Fog** | 3 | Hazard damage +50% per rank |
| 7 | **Mycelial Wrath** | 5 | Enemy attack speed +10% per rank |
| 8 | **Spore Drought** | 3 | Silk Gauge fill rate -20% per rank |
| 9 | **Symbiotic Decay** | 2 | Healing effectiveness -25% per rank |
| 10 | **Temporal Mold** | 2 | Room timer (5 min base, -30s per rank) |
| 11 | **Fusion Strain** | 2 | Fusion HP bonus reduced (-5% per rank) |
| 12 | **Echo Famine** | 3 | Echo currency drops -15% per rank |
| 13 | **Venomous Passage** | 3 | Transition rooms have enemies |
| 14 | **Haunted Echoes** | 2 | Dead enemies revive once at 50% HP |
| 15 | **The Rot Ascends** | 1 | Boss Phase 3 starts at 50% HP (not 33%) |

## Condition Combinations (Player-Curated Difficulty)

Players toggle conditions individually. Some powerful combos:

| Combo | Heat | Challenge |
|-------|------|-----------|
| Thick Membrane 5 + Aggressive 5 | 10 | Pure stat check, no mechanic changes |
| Withered Boons 3 + Echo Famine 3 | 6 | Resource starvation, must be efficient |
| Temporal Mold 2 + Haunted Echoes 2 | 4 | Speed + thoroughness pressure |
| All rank 1 | 15 | Breadth challenge, everything slightly harder |
| Max everything | 50 | "Impossible" tier, community achievement |

## Cosmetic Rewards

| Heat Milestone | Reward |
|---------------|--------|
| Heat 10 clear | Class-specific weapon skin (glowing) |
| Heat 20 clear | Infection VFX variant (crystalline) |
| Heat 30 clear | Canopy decoration (personal statue) |
| Heat 50 clear | Title: "Rot Sovereign" + unique Fusion skin |

## Leaderboard

Per-server leaderboard tracks:
- Highest Heat cleared (per class, per Crest)
- Fastest clear time at each Heat level
- Most Heat cleared in one session (cumulative)
