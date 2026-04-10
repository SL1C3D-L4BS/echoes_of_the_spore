# Balance Testing

## Overview

Balance testing ensures combat feels fair, classes are viable, boons provide meaningful
choices, and infection progression creates the intended risk/reward curve.

## Automated Balance Simulations

Run nightly: AI bots play thousands of sessions to generate statistical data.

| Simulation | Bot Count | Sessions | Metric Output |
|-----------|-----------|----------|--------------|
| Class DPS benchmark | 1 per class | 100 each | DPS at gear level 0, 5, 10 |
| Boss time-to-kill | 4 bots (mixed class) | 50 per boss | TTK mean, std dev, min, max |
| Boss kill rate | 4 bots (skill tiers) | 200 per boss | Win % per skill tier |
| Boon win rate impact | 4 bots | 1000 sessions | Win rate delta per boon |
| Infection death curve | 4 bots | 500 sessions | % deaths caused by infection |
| Heat scaling | 4 bots | 100 per heat | Completion rate per heat level |

## Class Balance Targets

| Class | Solo DPS (base) | Party DPS | Survivability | Utility |
|-------|----------------|-----------|--------------|---------|
| Bind | 180-220 /s | 160-200 /s | High (self-heal) | Medium |
| Weaver | 250-290 /s | 200-240 /s | Medium | High (buffs) |
| Striker | 320-360 /s | 300-340 /s | Low | Low |
| Warden | 140-180 /s | 120-160 /s | Highest (tank) | High (shields) |

### Balance Red Lines

| Metric | Acceptable Range | Action if Outside |
|--------|-----------------|------------------|
| Class pick rate | 15-35% each | Investigate, adjust in next patch |
| Class win rate | 45-55% | P1 balance ticket |
| Class DPS spread | Max 2x between lowest and highest | Rebalance |
| Any class "must pick" | Never > 50% pick rate in ranked | Nerf |

## Boss Balance Targets

| Boss | Target TTK (4-player) | Target Kill Rate | Attempts to Clear |
|------|----------------------|-----------------|------------------|
| Zone 1 Boss | 60-90s | 70% first attempt | 1-2 |
| Zone 2 Boss | 90-120s | 50% first attempt | 2-3 |
| Zone 3 Boss | 120-150s | 40% first attempt | 2-4 |
| Zone 4 Boss | 120-150s | 35% first attempt | 3-5 |
| Zone 5 Boss | 150-180s | 30% first attempt | 3-5 |
| Zone 6 Boss | 150-180s | 25% first attempt | 4-6 |
| Zone 7 Boss | 180-240s | 20% first attempt | 5-8 |
| Zone 8 Boss | 240-300s | 15% first attempt | 5-10 |

## Boon Balance Framework

### Rarity Power Budget

| Rarity | Power Points | Example |
|--------|-------------|---------|
| Common | 10-15 | +10% attack speed |
| Uncommon | 20-30 | +25% damage to poisoned enemies |
| Rare | 35-50 | Dash deals 100 damage |
| Epic | 55-75 | Critical hits heal 15% of damage |
| Legendary | 80-100 | Every 5th hit deals 3x damage |

### Boon Win Rate Analysis

```
For each boon B:
  win_rate_with_B = sessions_won_with_B / sessions_played_with_B
  baseline_win_rate = overall_win_rate
  delta = win_rate_with_B - baseline_win_rate

  If delta > +15%: boon is overpowered → nerf
  If delta < -10%: boon is underpowered → buff
  If pick_rate < 5%: boon is unappealing → redesign
  If pick_rate > 40%: boon is must-pick → nerf or add alternatives
```

## Infection Balance Targets

| Metric | Target | Rationale |
|--------|--------|-----------|
| Avg infection at death | 40-60 | Mid-range, meaningful tradeoff |
| % runs reaching tier 76+ | 15-25% | Rare but achievable |
| % runs reaching tier 91+ | 3-8% | Very rare, comedy reward |
| % deaths caused by infection overload | 10-20% | Meaningful risk |
| Infection gain rate (per minute) | 5-8 points | ~12-16 min to reach 100 |

## Heat Scaling Balance

| Heat Level | Enemy HP Mult | Enemy DMG Mult | Target Completion Rate |
|-----------|-------------|---------------|----------------------|
| 0 (Normal) | 1.0x | 1.0x | 60% |
| 1 | 1.15x | 1.1x | 50% |
| 2 | 1.3x | 1.2x | 40% |
| 3 | 1.5x | 1.3x | 30% |
| 5 | 1.8x | 1.5x | 20% |
| 7 | 2.2x | 1.8x | 10% |
| 10 | 3.0x | 2.5x | 3% |

## Playtest Data Collection

During human playtests, collect additional qualitative data:

| Data Point | Collection Method |
|-----------|------------------|
| "Fun factor" rating (1-10) | Post-session survey |
| "Felt unfair" moments | In-session flagging (hotkey) |
| Boss difficulty rating | Post-boss survey prompt |
| Boon preference reasoning | Post-session interview |
| Infection anxiety level | Post-session survey |
| Fusion satisfaction | Post-session survey |
| Session length preference | Observed + survey |

## Balance Patch Cadence

| Phase | Frequency | Scope |
|-------|-----------|-------|
| Alpha | Weekly | Aggressive changes, +-30% values |
| Beta | Bi-weekly | Moderate changes, +-15% values |
| Launch | Monthly | Conservative, +-5-10% values |
| Live | Seasonal | Major rebalance with new content |
| Hotfix | As needed | Emergency nerf for broken combos |

## Tools

| Tool | Purpose |
|------|---------|
| Boon Balance Inspector (Editor) | View all boon stats in spreadsheet format |
| DPS Calculator (spreadsheet) | Theoretical DPS per class/loadout |
| Sim Runner (CLI) | Run N bot sessions, output CSV stats |
| Balance Dashboard (Grafana) | Live metrics from player sessions |
