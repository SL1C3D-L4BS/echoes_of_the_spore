# Combo System

## Overview

Sequential hits within a timing window build a combo counter that amplifies damage, infection gain, and audiovisual intensity. The system rewards sustained aggression while punishing recklessness through strict break conditions.

## Combo Window

| Parameter | Value | Notes |
|-----------|-------|-------|
| Window duration | 500 ms (30 ticks) | Time between hits to maintain combo |
| Counter range | 0-99 | Display caps at 99, internal counter uncapped |
| Damage bonus | +1% per hit | Multiplicative with other damage modifiers |
| Damage cap | +30% (at 30 hits) | Diminishing returns beyond 30 |
| Infection bonus | +0.5% per 10 hits | Rounds down; 9 hits = +0%, 10 hits = +0.5% |
| Silk Gauge bonus | None | Silk fill rate unchanged by combo |

## Damage Scaling Table

| Combo Hits | Damage Multiplier | Infection Bonus | Tier |
|------------|-------------------|-----------------|------|
| 0 | 1.00x | +0.0% | None |
| 1-4 | 1.01-1.04x | +0.0% | Bronze |
| 5-9 | 1.05-1.09x | +0.0% | Bronze |
| 10-14 | 1.10-1.14x | +0.5% | Silver |
| 15-19 | 1.15-1.19x | +0.5% | Silver |
| 20-24 | 1.20-1.24x | +1.0% | Gold |
| 25-29 | 1.25-1.29x | +1.0% | Gold |
| 30+ | 1.30x (capped) | +1.5% | Prismatic |

## Combo Tiers and Feedback

| Tier | Hit Range | Screen Shake | Hit Sound Pitch | Counter Color | VFX |
|------|-----------|-------------|-----------------|---------------|-----|
| None | 0 | None | 1.0x | Hidden | None |
| Bronze | 1-9 | 0.5 px | 1.0-1.02x | White (#E8E4D9) | Faint spark on hit |
| Silver | 10-19 | 1.0 px | 1.03-1.06x | Silver (#C0C0C0) | Streak trail behind weapon |
| Gold | 20-29 | 2.0 px | 1.07-1.10x | Gold (#C9A84C) | Ring burst per hit |
| Prismatic | 30+ | 3.0 px | 1.12x (capped) | Cycling rainbow | Full-screen pulse, particle shower |

## Screen Shake Specification

```
shake_intensity = tier_base_px × (1.0 + combo_hits × 0.02)
shake_duration = 2 frames (33 ms)
shake_decay = linear
max_shake = 4.0 px (hard cap, prevents readability loss)
```

## Audio Feedback

| Event | Sound | Pitch Modifier |
|-------|-------|----------------|
| Hit (combo active) | `sfx_combo_hit_{tier}` | `1.0 + (combo_count × 0.004)`, max 1.12 |
| Tier promotion | `sfx_combo_tier_up` | Fixed, unique per tier |
| Combo break | `sfx_combo_break` | Pitch down proportional to lost tier |
| 30-hit milestone | `sfx_combo_prismatic` | Fixed fanfare, plays once |

## Combo Break Conditions

| Condition | Combo Result | Notes |
|-----------|-------------|-------|
| 500 ms without hitting | Reset to 0 | Standard timeout |
| Player takes damage | Reset to 0 | Any damage source, including self-inflicted |
| Enter Rest room | Reset to 0 | Intentional design: Rest = safe reset |
| Room transition | Preserved | Combo carries between combat rooms |
| Fusion merge/unmerge | Preserved | Combo persists across Fusion state |
| Player death (overload) | Reset to 0 | Overload clears all ephemeral state |
| Healing (Bind action) | Preserved | No penalty for healing mid-combo |

## Multiplayer Combo Rules

| Scenario | Behavior |
|----------|----------|
| Solo | Single counter, straightforward |
| Co-op (unfused) | Each player has independent counter |
| Fused state | Shared counter, both players' hits contribute |
| Spore Plan active | All participating players share a temporary counter |

## Integration with Other Systems

- **Damage Model:** Combo multiplier sits after ClassMultiplier, before CritMultiplier in the formula
- **Status Effects:** Each combo hit can apply status independently (no stacking penalty)
- **Infection:** Infection bonus from combo is additive with base infection rate
- **Echo Codex:** Mastery entries track highest combo achieved per class
- **Heat System:** No direct interaction; combo difficulty unchanged by Heat

## ECS Implementation

```csharp
public struct ComboComponent
{
    public int HitCount;            // Current combo counter
    public int TicksSinceLastHit;   // Countdown to break (30 ticks max)
    public ComboTier CurrentTier;   // enum: None, Bronze, Silver, Gold, Prismatic
    public FixedPoint64 DamageMultiplier; // Cached: min(1.30, 1.0 + hits * 0.01)
}
// Processed by ComboSystem each tick
// Resets via ComboBreakSystem on damage/timeout/rest
```
