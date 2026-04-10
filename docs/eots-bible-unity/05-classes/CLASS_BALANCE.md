# CLASS BALANCE -- Framework and Methodology

## Design Pillars

1. Every class is viable solo and in co-op through endgame.
2. No Crest is strictly dominant within its class.
3. Skill expression matters more than build optimization.
4. Fused classes amplify strengths, not eliminate weaknesses.

## DPS Targets (Per Second, Sustained)

Measured against Training Dummy with base stats, no boons, default Crest, continuous engagement.

| Class | Target DPS | Burst DPS (5s window) | Sustained DPS (60s) |
|---|---|---|---|
| Sporeling | 28-32 | 45-52 | 26-30 |
| Rootwarden | 16-20 | 22-28 | 18-22 |
| Bloomcaster | 34-40 | 55-65 | 30-36 |
| Decayer | 12-16 | 18-22 | 14-18 |

### DPS by Crest

| Class | Crest | Target DPS Modifier |
|---|---|---|
| Sporeling | Swift | +10% (combo uptime) |
| Sporeling | Phantom | +25% burst / -15% sustained |
| Sporeling | Thorn | -5% early / +20% at full ramp |
| Rootwarden | Sentinel | +5% (stun enables free hits) |
| Rootwarden | Bulwark | -10% (reactive, not proactive) |
| Rootwarden | Bramble | +15% (raw slam damage) |
| Bloomcaster | Artillery | +20% burst / -10% sustained |
| Bloomcaster | Bloom | -5% single target / +30% AoE |
| Bloomcaster | Weaver | -20% (healing offsets damage loss) |
| Decayer | Catalyst | -30% personal / +10% team total |
| Decayer | Plague | +40% (DoT stacking) |
| Decayer | Leech | +25% personal / 0% team |

## Time-to-Kill Targets

TTK against standard enemy archetypes (base stats, no boons, default Crest).

| Enemy Type | HP | Sporeling TTK | Rootwarden TTK | Bloomcaster TTK | Decayer TTK |
|---|---|---|---|---|---|
| Minion | 20 | 0.7s | 1.1s | 0.5s | 1.4s |
| Standard | 50 | 1.7s | 2.8s | 1.4s | 3.5s |
| Elite | 120 | 4.0s | 6.5s | 3.2s | 8.0s |
| Mini-Boss | 300 | 10.0s | 16.0s | 8.5s | 20.0s |
| Boss (Phase) | 800 | 27.0s | 42.0s | 22.0s | 52.0s |

**Design Rule:** No class should TTK a Standard enemy in under 1.0s or over 5.0s at base stats.

## Survivability Ratings

| Metric | Sporeling | Rootwarden | Bloomcaster | Decayer |
|---|---|---|---|---|
| Effective HP (base) | 5 | 8 | 4 | 6 |
| Effective HP (max) | 8 | 12 | 6 | 9 |
| Damage Reduction | 0.8x | 1.4x | 0.6x | 1.0x |
| Stagger Threshold | 2 hits | 5 hits | 1 hit | 3 hits |
| Evasion Rating | High | Low | Medium | Medium |
| Self-Heal Access | Low (Symbiote Strike) | Medium (Fungal Regen) | Low (Photosynthesis) | High (Entropy Touch, Siphon) |
| Effective Survivability Score | 6.5/10 | 9.0/10 | 3.5/10 | 7.0/10 |

**Design Rule:** Survivability x DPS product should fall within 180-240 for each class to maintain balance.

| Class | DPS (mid) | Survivability | Product |
|---|---|---|---|
| Sporeling | 30 | 6.5 | 195 |
| Rootwarden | 18 | 9.0 | 162 (compensated by CC utility) |
| Bloomcaster | 37 | 3.5 | 130 (compensated by range safety) |
| Decayer | 14 | 7.0 | 98 (compensated by team force multiplication) |

## Role Effectiveness Matrix

How effective each class is at each combat role (1-5 scale).

| Role | Sporeling | Rootwarden | Bloomcaster | Decayer |
|---|---|---|---|---|
| Single-Target DPS | 4 | 2 | 5 | 1 |
| AoE DPS | 3 | 3 | 4 | 3 |
| Crowd Control | 1 | 5 | 2 | 4 |
| Survivability | 2 | 5 | 1 | 3 |
| Team Support | 1 | 3 | 2 | 5 |
| Mobility | 5 | 1 | 3 | 2 |
| Boss DPS | 4 | 2 | 5 | 2 |
| Room Clear | 3 | 3 | 4 | 3 |

**Design Rule:** Each class should have at least one role rated 5 and at least one rated 1.

## Matchup Balance (PvP / Competitive Co-op)

Target: no matchup worse than 35:65. Current projections:

| Matchup | Favored | Ratio | Notes |
|---|---|---|---|
| Sporeling vs Rootwarden | Sporeling | 55:45 | Mobility advantage, but Anchor punishes |
| Sporeling vs Bloomcaster | Sporeling | 60:40 | Gap-close dominant, Bloomcaster needs perfect spacing |
| Sporeling vs Decayer | Decayer | 45:55 | Entropy Touch punishes melee |
| Rootwarden vs Bloomcaster | Rootwarden | 60:40 | HP absorbs poke, pull closes gap |
| Rootwarden vs Decayer | Even | 50:50 | Attrition mirror |
| Bloomcaster vs Decayer | Bloomcaster | 55:45 | Range advantage |

## Balance Testing Methodology

### Automated Testing

- **DPS Benchmark:** Automated dummy test, 60s sustained, all Crests. Run nightly.
- **TTK Validation:** Against all enemy archetypes. Flag any class outside target range.
- **Survivability Sim:** Simulate 100 encounters per class vs each enemy type. Track death rates.

### Manual Testing

- **Playtest Sessions:** Weekly 2-hour sessions, 4 testers, all class/Crest combinations rotated.
- **Matchup Testing:** Head-to-head duels, 10 rounds per matchup, track win rates.
- **Fusion Testing:** Each of 12 fusions tested in 3 boss encounters per session.

### Metrics Tracked

| Metric | Acceptable Range | Red Flag |
|---|---|---|
| Class pick rate | 18-32% | Below 15% or above 35% |
| Class win rate (solo) | 45-55% | Below 40% or above 60% |
| Crest pick rate (within class) | 25-45% | Below 15% or above 55% |
| Fusion pick rate | 5-15% per fusion | Below 3% or above 20% |
| Average run completion time | 25-40 min | Below 20 or above 50 min |
| Boss TTK variance (across classes) | Within 30% of median | Above 50% variance |

## Patch Cadence

| Patch Type | Frequency | Scope |
|---|---|---|
| Hotfix | As needed (24-48h) | Critical bugs, game-breaking imbalance |
| Balance Patch | Biweekly | Number tuning (damage, HP, cooldowns, durations) |
| Crest Patch | Monthly | Crest-specific adjustments, slot layout changes |
| Function Patch | Monthly (with Crest) | Function mode rebalancing |
| Boon Patch | Every 6 weeks | Boon rarity weights, effect tuning, new Duo/Legendary |
| Major Update | Quarterly | New Functions, new Crests, new Civilizations |

## Balance Change Rules

1. Never change more than 3 values for a single class in one patch.
2. Prefer Crest-level changes over base-class changes.
3. Buff underperformers before nerfing overperformers.
4. All changes must include revert criteria (if metric X returns to Y, revert).
5. Community sentiment is input, not authority. Data drives decisions.
6. Fusion balance is tuned via component classes, never via fusion-specific overrides.
