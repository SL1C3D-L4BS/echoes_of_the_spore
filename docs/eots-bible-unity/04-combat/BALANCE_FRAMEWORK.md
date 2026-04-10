# Balance Framework

## Overview

Balance in Echoes targets a specific DPS-to-TTK (time to kill) ratio per class, with structured testing methodology and defined acceptable variance. This document is the source of truth for all balance targets and the process to maintain them.

## DPS Targets by Class

All values assume Heat 0, no boons, no combo bonus, no Infection multiplier. Base kit only.

### Sustained DPS (optimal play, Light chain)

| Class | Light Chain DPS | Heavy Weave DPS | Dash Attack Opener DPS | Notes |
|-------|----------------|-----------------|----------------------|-------|
| Sporeling | 32 dmg/sec | 38 dmg/sec | 36 dmg/sec | Balanced baseline |
| Rootwarden | 27 dmg/sec | 33 dmg/sec | 28 dmg/sec | Lowest DPS, highest survivability |
| Bloomcaster | 38 dmg/sec | 45 dmg/sec | 34 dmg/sec | Glass cannon, highest raw output |
| Decayer | 22 dmg/sec | 26 dmg/sec | 20 dmg/sec | Debuff DPS not included (see effective DPS) |

### Effective DPS (including status effects and debuffs)

| Class | Solo Effective DPS | Co-op Effective DPS | Notes |
|-------|-------------------|--------------------|----|
| Sporeling | 36 dmg/sec | 38 dmg/sec | Phase Walk backstab uptime ~15% |
| Rootwarden | 30 dmg/sec | 35 dmg/sec | Aggro draw increases partner DPS |
| Bloomcaster | 44 dmg/sec | 50 dmg/sec | AoE increases in group scenarios |
| Decayer | 35 dmg/sec | 48 dmg/sec | Corrode + Bloom enable partner damage |

## Time-to-Kill Targets

### Player vs. Enemy TTK

| Enemy Tier | Solo TTK (Sporeling) | Target Range | Notes |
|-----------|---------------------|--------------|-------|
| Tier 1 | 2-3 seconds | 1.5-4.0s | Fodder, dispatched quickly |
| Tier 2 | 5-8 seconds | 4.0-10.0s | Moderate threat |
| Tier 3 (Mini-boss) | 15-25 seconds | 12.0-30.0s | Significant encounter |
| Zone Boss (Phase total) | 90-150 seconds | 60-180s | Core engagement |

### Enemy vs. Player TTK (at Heat 0)

| Enemy Tier | Hits to Kill Player (Sporeling, 12 HP) | Target Range |
|-----------|---------------------------------------|--------------|
| Tier 1 | 6-8 hits | 5-10 hits |
| Tier 2 | 4-5 hits | 3-6 hits |
| Tier 3 | 3-4 hits | 2-5 hits |
| Zone Boss | 3 hits | 2-4 hits |

## Balance Levers (Tuning Knobs)

| Lever | Range | Effect | Tuning Priority |
|-------|-------|--------|----------------|
| BaseDamage per attack | 1-50 | Direct DPS change | Primary |
| ClassMultiplier | 0.5-1.5 | Global class DPS scaling | Primary |
| Attack frame data (startup/recovery) | +/- 2 frames | DPS throughput via speed | Secondary |
| Enemy HP | 10-5000 | TTK adjustment | Primary |
| Enemy damage | 1-8 | Player TTK adjustment | Primary |
| Status effect duration/stacks | +/- 20% | Effective DPS shift | Secondary |
| Crit chance/multiplier | 0-20% / 1.0-2.0x | Burst DPS variance | Tertiary |

## Acceptable Variance

| Metric | Acceptable Range | Action Threshold |
|--------|-----------------|-----------------|
| Class DPS spread (effective) | Max 40% gap between highest and lowest | Retune if gap exceeds 40% |
| Solo vs. co-op TTK ratio | Co-op TTK should be 60-80% of solo | Retune HP scaling if outside |
| Heat 50 clear rate | 1-5% of attempts | Buff if < 0.5%, nerf if > 10% |
| Heat 0 clear rate (first clear) | 20-40% of attempts | Buff if < 15%, nerf if > 50% |
| Overload rate (1st overload) | 40-60% of runs | Core loop depends on this |
| True death rate | 15-30% of runs | Too high = frustration, too low = no stakes |

## Balance Testing Methodology

### Automated Testing

| Test Type | Frequency | Method |
|-----------|-----------|--------|
| DPS simulation | Every build | Bot plays optimal combo sequences against stationary target |
| TTK regression | Every build | Bots clear each enemy tier, record TTK |
| Heat scaling curve | Weekly | Bots attempt Heat 0-50, record clear rate |
| Status effect math | Every build | Unit tests verify all multiplier interactions |

### Playtest Protocol

| Session Type | Frequency | Participants | Focus |
|-------------|-----------|-------------|-------|
| Internal (dev team) | 2x/week | 4-8 devs | New content, feel check |
| QA structured | 1x/week | QA team | Edge cases, exploits |
| External (closed beta) | Monthly | 50-200 players | Clear rates, player sentiment |
| Data review | Weekly | Design lead | Telemetry dashboard analysis |

### Telemetry Metrics Tracked

| Metric | Granularity | Alert Condition |
|--------|-------------|-----------------|
| DPS per class per Heat level | Per-run | > 2 std dev from target |
| TTK per enemy tier | Per-encounter | Outside target range for 48 hours |
| Clear rate per Heat level | Daily aggregate | Outside acceptable variance |
| Overload rate per zone | Per-zone | > 70% or < 20% |
| Most/least picked boons | Weekly | Any boon < 2% or > 25% pick rate |
| Function usage distribution | Weekly | Any Function < 5% equip rate |

## Patch Cadence

| Patch Type | Frequency | Scope |
|-----------|-----------|-------|
| Hotfix | As needed (< 24h turnaround) | Game-breaking exploits, crashes |
| Balance tuning | Bi-weekly | Number adjustments (damage, HP, timings) |
| Content patch | Monthly | New boons, enemies, Functions |
| Season update | Every 90 days | New zone, boss, Battle Pass, meta shift |

### Balance Change Communication

| Change Size | Player-Facing Note |
|-------------|-------------------|
| < 5% adjustment | Stealth change, no note |
| 5-15% adjustment | Patch notes, brief explanation |
| > 15% adjustment | Patch notes + dev blog explaining rationale |
| Mechanic rework | Patch notes + dev blog + community discussion period |

## Class Balance Philosophy

| Class | Role | Target Solo Difficulty | Target Co-op Value |
|-------|------|----------------------|-------------------|
| Sporeling | Versatile striker | Medium | Medium (self-sufficient) |
| Rootwarden | Tanky frontline | Easy (high HP) | High (aggro draw, shields) |
| Bloomcaster | Ranged glass cannon | Hard (low HP) | High (AoE damage) |
| Decayer | Debuff support | Hard (low damage solo) | Very High (enables team) |
