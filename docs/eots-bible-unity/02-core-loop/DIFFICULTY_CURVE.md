# Difficulty Curve Design

## Zone Difficulty Progression

```
Difficulty ▲
    100%   │                                    ████ Zone 8
     90%   │                                ████
     80%   │                            ████ Zone 7
     70%   │                        ████ Zone 6
     60%   │                    ████ Zone 5
     50%   │                ████ Zone 4
     40%   │            ████ Zone 3
     30%   │        ████ Zone 2
     20%   │    ████ Zone 1
     10%   │████
      0%   └────────────────────────────────────── Progression
           Hub  Z1    Z2    Z3    Z4    Z5    Z6    Z7    Z8
```

## Per-Zone Difficulty Parameters

| Zone | Enemy Tier Mix | Enemy Count/Room | Hazard Density | Boon Quality | Clear Time |
|------|---------------|-----------------|----------------|-------------|------------|
| 1 Sporehaven | 90% T1, 10% T2 | 5-8 | Low (1-2) | Common-Uncommon | 4-8 min |
| 2 Cortex | 75% T1, 25% T2 | 6-10 | Medium (2-3) | Common-Uncommon | 6-10 min |
| 3 Fermentation | 60% T1, 35% T2, 5% T3 | 8-12 | Medium-High (3-4) | Uncommon-Rare | 8-15 min |
| 4 Chitinhall | 50% T1, 40% T2, 10% T3 | 10-15 | High (3-5) | Uncommon-Rare | 10-18 min |
| 5 Rot Garden | 40% T1, 45% T2, 15% T3 | 8-12 | High + Decay timer | Rare | 12-20 min |
| 6 Myco-Forge | 35% T1, 45% T2, 20% T3 | 10-14 | High (4-5) | Rare | 15-25 min |
| 7 Necroveil | 30% T1, 40% T2, 25% T3, Hunters | 8-10 + Hunters | Extreme | Rare-Heroic | 20-35 min |
| 8 Core | AI Director controlled | Dynamic | Dynamic | Dynamic | 25-45 min |

## Heat Difficulty Multipliers

| Heat Range | Enemy HP | Enemy Damage | Enemy Speed | Hazard Intensity | Echo Multiplier |
|-----------|---------|-------------|------------|------------------|----------------|
| 0 | 1.0x | 1.0x | 1.0x | 1.0x | 1.0x |
| 1-10 | 1.1x | 1.1x | 1.0x | 1.0x | 1.5x |
| 11-20 | 1.3x | 1.2x | 1.1x | 1.2x | 2.0x |
| 21-30 | 1.5x | 1.4x | 1.2x | 1.4x | 2.5x |
| 31-40 | 1.8x | 1.6x | 1.3x | 1.6x | 3.0x |
| 41-50 | 2.2x | 2.0x | 1.5x | 2.0x | 3.5x |

## Target Failure Rates

| Scenario | Zone 1-2 | Zone 3-4 | Zone 5-6 | Zone 7-8 |
|----------|---------|---------|---------|---------|
| First-time player | 30% | 60% | 85% | 95% |
| 10-hour player | 10% | 30% | 50% | 70% |
| 50-hour player | 2% | 10% | 25% | 40% |
| Heat 0 veteran | 1% | 3% | 10% | 20% |
| Heat 20 veteran | 5% | 15% | 30% | 50% |
| Heat 50 (max) | 20% | 40% | 60% | 80% |

## Rubber-Banding Mechanics

### After 3 consecutive deaths (same zone):
- Boon offering quality increases by 1 tier
- Rest rooms heal 1 additional HP
- Tier 3 spawn rate reduced by 50%

### After 5 consecutive clears (same zone, same Heat):
- No adjustment (player is skilled, don't punish)
- Suggest increasing Heat via UI prompt

### AI Director (Zone 8 only):
- Targets 30-40% room failure rate dynamically
- Inputs: infection level, boon loadout, death rate, Heat, time in room
- Adjusts: enemy composition, hazard intensity, boon quality, boss phase speed
