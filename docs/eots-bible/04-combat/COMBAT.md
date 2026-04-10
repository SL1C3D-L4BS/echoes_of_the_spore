# 04 — COMBAT ARCHITECTURE & FEEL TARGETS

## Design Philosophy

Combat in Echoes follows the **Committed Aggression** philosophy: attacks have recovery frames you cannot cancel (commitment from Hollow Knight), but movement is fast and dashes are instant (aggression from Dead Cells). The result is a system where positioning and timing matter as much as reaction speed.

## Feel Targets (Quantified)

| Parameter | Value | Reference |
|---|---|---|
| Input-to-action latency | < 3 frames (50 ms) | Dead Cells benchmark |
| Dash startup frames | 0 (instant) | Dead Cells |
| Light attack startup | 4 frames (66 ms) | Hollow Knight nail swing |
| Light attack recovery | 8 frames (133 ms) | Slightly faster than HK |
| Heavy attack startup | 10 frames (166 ms) | Commitment cost |
| Heavy attack recovery | 16 frames (266 ms) | High commitment |
| Hitstop (on enemy hit) | 3 frames (50 ms) | Universal game feel |
| Hitstop (on player hit) | 5 frames (83 ms) | Emphasize danger |
| Knockback distance (light) | 1.5 tiles | Readable displacement |
| Knockback distance (heavy) | 3.0 tiles | Significant |
| I-frames on dash | 6 frames (100 ms) | Standard |
| I-frames on hit (player) | 20 frames (333 ms) | Generous for co-op |
| Coyote time | 6 frames (100 ms) | Standard platformer |
| Jump buffer | 6 frames (100 ms) | Standard platformer |
| Gravity (fall acceleration) | 0.6 FP64 units/tick² | Tuned for snappy feel |
| Max fall speed | 12.0 FP64 units/tick | Terminal velocity cap |
| Run speed | 4.5 FP64 units/tick | Responsive ground movement |
| Jump force | 12.0 FP64 units/tick | ~3.5 tile jump height |

## Damage Model

All damage is integer. No floating-point in the damage pipeline.

```
FinalDamage = BaseDamage
    × ClassMultiplier (from Crest)
    × BoonMultiplier (from active Echo Boons)
    × InfectionMultiplier (from infection tier)
    × CritMultiplier (1.0 or 2.0, based on crit chance roll)
    × EnemyDefenseReduction (from Corrode/debuffs)
    × HeatMultiplier (from Pact conditions on enemies)

All multipliers are FixedPoint64. Final result truncated to int32.
```

**Damage Number Display:** Damage numbers float upward from the hit point. Color-coded by source: white (normal), cyan (crit), gold (Duo Echo proc), red (enemy damage to player).

## Status Effect System

Eight status effects, one per civilization. Effects stack or have stack limits.

| Effect | Source Civ | Stack Behavior | Duration | Interaction |
|---|---|---|---|---|
| Petrify | Lithic | Binary (on/off) | 2 seconds | Target immobile, takes 2x damage |
| Daze | Luminari | Binary | 3 seconds | 30% miss chance on target attacks |
| Bloom | Verdant | Stacks (1-5) | 4 seconds per stack | DoT that spreads to enemies within 2 tiles at 3+ stacks |
| Corrode | Ferrox | Stacks (1-10) | 8 seconds per stack | -5% defense per stack |
| Shatter | Glacine | Stacks (1-5) | 6 seconds | At 5 stacks: AoE explosion (200% base damage, 3-tile radius) |
| Scorch | Pyreth | Stacks (1-10) | 5 seconds per stack | DoT: 3% base damage per stack per second |
| Void Mark | Umbral | Binary | 5 seconds | Backstab damage +50% |
| Harmonic | Resonant | Counter (1-4) | Resets on 3-second gap | At 4: next hit deals 3x damage, counter resets |

## Combo System

Hits within 500 ms of each other increment a combo counter. Combo counter affects:
- Damage: +1% per combo hit (caps at +30% at 30 hits)
- Infection gain: +0.5% per 10 combo hits
- Visual: screen shake intensity scales with combo
- Audio: hit sounds pitch up slightly per combo hit

Combo breaks on: taking damage, 500 ms without hitting, entering Rest room.

## Healing

Resource: Silk Gauge (named thematically, functions like Silksong's Silk / Hollow Knight's Soul).
- Filled by dealing damage (each hit = 5% fill)
- Spend 100% to heal 3 HP (Bind action — can be used mid-air, 0.5s animation)
- Alternatively spend 50% on a Weaver Technique (class-specific power move)
- Cannot heal and attack simultaneously. Healing is a tactical commitment.

## Death & Overload

When HP reaches 0:
1. First overload: HP refills to 100%. One random Active Function is disabled. Screen flash + unique audio sting. Mycelium Narrator comments.
2. Second overload: Same, second Function disabled.
3. Third overload: True death. Return to Canopy. Echoes earned at 50% rate.

Disabled Functions restore at the next Rest room that is 3+ rooms away from the overload location. This prevents trivial recovery.
