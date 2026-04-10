# Per-Run Progression (Ephemeral)

## Overview

Each delve is a self-contained run with four ephemeral progression axes. All per-run state resets on true death or successful completion (except Echoes earned, which persist at full or 50% rate). This document specifies every resource, accumulation rate, and reset condition.

## Progression Axes

| Axis | Type | Accumulation | Reset Condition |
|------|------|-------------|-----------------|
| Echo Boons | Power-ups | Acquired at offerings/shops | True death: lost. Completion: lost. |
| Infection | Risk/reward meter | Passive + active sources | True death: preserved. Completion: preserved. Purifiable. |
| Silk Gauge | Combat resource | Filled by dealing damage | Room transition: preserved. Rest room: reset to 0. |
| Combo Counter | Damage multiplier | Built by consecutive hits | Timeout/damage/rest: reset to 0. |

## Echo Boons (Ephemeral Power-Ups)

### Acquisition Sources

| Source | Boons Offered | Frequency |
|--------|--------------|-----------|
| Room clear offering | 3 choices (1 pick) | Every 3 rooms |
| Boss kill offering | 3 choices (Heroic tier) | After each boss |
| Shop purchase | 2-4 available | Rest rooms with merchant |
| Challenge room reward | 1 guaranteed (Rare+) | Optional rooms |

### Boon Rarity Distribution

| Rarity | Offering Weight | Power Level | Visual Indicator |
|--------|----------------|-------------|-----------------|
| Common | 60% | 1.0x | White border |
| Uncommon | 25% | 1.5x | Green border |
| Rare | 12% | 2.5x | Blue border |
| Heroic | 3% | 4.0x | Purple border |

### Boon Slot Limits

| Parameter | Value |
|-----------|-------|
| Max boons carried | 12 |
| Boons at overflow | Must sacrifice 1 existing boon to accept new |
| Boon reroll cost | 50 Echoes (first reroll free per offering) |
| Boon upgrade (3 of same civ) | Automatically upgrades lowest-rarity boon of that civ by 1 tier |

### Heat Interaction

| Heat Condition | Effect on Boons |
|---------------|-----------------|
| Withered Boons (rank 1) | No Heroic boons appear |
| Withered Boons (rank 2) | No Rare+ boons appear |
| Withered Boons (rank 3) | No Uncommon+ boons appear |
| Echo Famine (per rank) | -1 boon choice per offering (min 1) |

## Infection Accumulation (Per-Run)

### Sources and Rates

| Source | Rate | Cumulative Impact |
|--------|------|-------------------|
| Room clear (passive) | +2 per room | ~60 Infection across 30-room delve |
| Taking damage | +1 per hit | Varies; ~10-30 per run |
| Combo bonus | +0.5% per 10 hits | Marginal; ~2-5 per run |
| Infected enemies | +3 per kill | Zone-dependent; 0-15 per run |
| Boss kills | +5 per boss | +40 across 8 bosses |
| Environmental hazards | +5 per tick | Avoidable |

### Infection Curve (Expected Per-Run)

| Run Stage | Rooms Cleared | Expected Infection | Tier |
|-----------|--------------|-------------------|------|
| Zone 1-2 | 1-8 | 16-24 | Tier 1 (Touched) |
| Zone 3-4 | 9-16 | 34-48 | Tier 2 (Tainted) |
| Zone 5-6 | 17-24 | 50-72 | Tier 3 (Corrupted) |
| Zone 7-8 | 25-32 | 68-96 | Tier 4 (Consumed) |

### Mid-Run Purification

| Method | Cost | Infection Removed | Availability |
|--------|------|-------------------|-------------|
| Rest room purify | 50 Echoes | 10 points | Any Rest room |
| Purification Boon | Boon slot | 15 points (on pickup) | Random offering |
| Verdant shrine | Free | 5 points | Rare room event |

## Silk Gauge (Per-Room Combat Resource)

| Parameter | Value |
|-----------|-------|
| Capacity | 100 units |
| Fill per light hit | 5 units |
| Fill per heavy hit | 8 units |
| Fill per dash attack | 4 units |
| Fill per Fusion hit | 7 units |
| Fill per DoT tick | 1 unit |
| Reset on Rest room | Yes (to 0) |
| Reset on room transition | No (preserved) |
| Reset on overload | Yes (to 0) |

### Silk Economy Per Room

| Room Type | Expected Hits | Expected Silk Earned | Spend Opportunities |
|-----------|--------------|---------------------|-------------------|
| Small combat (5 enemies) | 15-20 | 75-100 | 1 Bind or 1-2 Weaver |
| Medium combat (10 enemies) | 30-40 | 150-200 | 2-3 Binds or 3-4 Weavers |
| Boss room | 80-120 | 400-600 | 4-6 Binds or 8-12 Weavers |

## Combo Counter (Per-Encounter Scaling)

| Parameter | Value |
|-----------|-------|
| Window | 500 ms between hits |
| Damage bonus | +1% per hit (cap +30%) |
| Infection bonus | +0.5% per 10 hits |
| Break conditions | Timeout, damage taken, Rest room |
| Carry between rooms | Yes |
| Reset on overload | Yes |

### Expected Combo Ranges

| Scenario | Typical Max Combo | Damage Bonus |
|----------|------------------|-------------|
| Small room (solo) | 8-15 | +8-15% |
| Medium room (solo) | 15-25 | +15-25% |
| Boss fight (solo) | 20-40 | +20-30% (capped) |
| Co-op (fused, medium room) | 25-50 | +25-30% (capped) |

## Run Summary (End of Delve)

| Data Point | Display |
|-----------|---------|
| Echoes earned (base) | Total before multipliers |
| Heat multiplier | Applied to Echoes |
| Infection at end | Carried to account |
| Boons collected | List with rarity |
| Highest combo | Per-class leaderboard submission |
| Overloads taken | Count and locations |
| Time elapsed | Speedrun leaderboard submission |
| Community Tree contribution | Post-run branch selection prompt |
