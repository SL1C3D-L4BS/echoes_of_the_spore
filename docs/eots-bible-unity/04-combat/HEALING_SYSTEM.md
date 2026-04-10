# Healing System (Silk Gauge)

## Design Intent

Healing is a tactical commitment. The Silk Gauge forces a binary decision each time it fills: heal HP (survive) or spend on a Weaver Technique (power). There is no passive regeneration. Every point of health is earned through aggression.

## Silk Gauge Parameters

| Parameter | Value | Notes |
|-----------|-------|-------|
| Gauge capacity | 100 units | Displayed as percentage bar |
| Fill per hit (light) | 5 units | All light attacks, ground or air |
| Fill per hit (heavy) | 8 units | Reward commitment |
| Fill per hit (dash attack) | 4 units | Slightly lower, fast attack |
| Fill per status tick (DoT) | 1 unit | Bloom/Scorch ticks fill slowly |
| Fill per Fusion hit | 7 units | Encourages Fusion aggression |
| Fill rate cap | None | No diminishing returns on fill |
| Drain on room transition | 0 | Gauge persists between rooms |
| Drain on Rest room entry | Full reset to 0 | Intentional: Rest is a clean state |

## Bind Heal (Spend 100%)

| Parameter | Value | Notes |
|-----------|-------|-------|
| Silk cost | 100 units (full gauge) | All-or-nothing spend |
| HP restored | 3 HP | Out of ~12-20 HP max depending on class |
| Cast time | 0.5 seconds (30 ticks) | Vulnerable during animation |
| Startup frames | 4 frames | Brief wind-up before heal applies |
| Recovery frames | 12 frames | Cannot act during recovery |
| Cancel window | First 2 frames only | Can dash-cancel before heal commits |
| Usable mid-air | Yes | No ground requirement |
| i-frames during heal | 0 | Fully vulnerable, must choose safe moment |
| Heal VFX | Green silk threads wrap character | Duration matches cast time |
| Heal SFX | `sfx_bind_heal` | Soft chime + thread sound |

## Weaver Technique (Spend 50%)

| Parameter | Value | Notes |
|-----------|-------|-------|
| Silk cost | 50 units (half gauge) | Leaves 50 remaining or 0 if gauge was exactly 50 |
| Effect | Class-specific power ability | See table below |
| Cast time | Varies by class | 0.3-0.8 seconds |
| Cooldown | None (gated by Silk cost) | Can use twice per full gauge |

### Weaver Techniques by Class

| Class | Technique Name | Effect | Cast Time | Damage |
|-------|---------------|--------|-----------|--------|
| Sporeling | Phase Burst | Teleport 5 tiles + AoE (2-tile radius) | 0.3s | 150% base |
| Rootwarden | Ironbark Shell | 3-second damage shield (absorbs 8 HP) | 0.4s | N/A |
| Bloomcaster | Spore Nova | 360-degree projectile ring (8 projectiles) | 0.5s | 80% base each |
| Decayer | Entropy Field | 3-tile AoE debuff zone (4s duration, -30% enemy speed) | 0.6s | 50% base/sec |

## Tactical Commitment Design

The Silk system enforces meaningful choices at multiple levels:

| Decision Point | Options | Tradeoff |
|---------------|---------|----------|
| Gauge at 50% | Weaver Technique now vs. save for Bind | Power now vs. survival later |
| Gauge at 100% | Bind heal vs. 2x Weaver Technique | 3 HP vs. two power moves |
| Low HP, gauge filling | Aggressive play to fill vs. retreat | Risk death for heal vs. safety |
| Full HP, full gauge | Hold gauge vs. Weaver Technique | Insurance vs. immediate power |
| Fused state | Shared gauge, shared decision | Both players must agree on spend |

## Heat System Interactions

| Heat Condition | Effect on Silk |
|---------------|----------------|
| Spore Drought (per rank) | Fill rate -20% per rank |
| Symbiotic Decay (per rank) | Bind heal restores 1 fewer HP per rank |
| No Heat conditions | Standard behavior |

## Infection Interactions

| Infection Tier | Effect on Silk |
|---------------|----------------|
| 0-25 | No change |
| 26-50 | Bind animation +0.1s (hesitation) |
| 51-75 | Bind heal -1 HP (corruption tax) |
| 76-100 | Weaver Technique gains +25% damage but costs 65 Silk |

## Multiplayer Silk Rules

| Scenario | Silk Behavior |
|----------|--------------|
| Solo | Standard gauge |
| Co-op (unfused) | Independent gauges |
| Fused state | Shared gauge, either player can spend |
| Spore Plan | No direct interaction with Silk |

## Visual Feedback

| Gauge State | Bar Color | Character Effect |
|-------------|-----------|-----------------|
| 0-24% | Dark green (#1A4D2E) | None |
| 25-49% | Green (#2ECC71) | Faint silk threads on hands |
| 50-74% | Bright green (#27AE60) | Silk threads pulse, Weaver ready indicator |
| 75-99% | Yellow-green (#A8D948) | Threads glow, subtle hum SFX |
| 100% | White (#E8E4D9) | Full-body silk aura, Bind ready chime |

## ECS Implementation

```csharp
public struct SilkGaugeComponent
{
    public int CurrentSilk;      // 0-100
    public int MaxSilk;          // 100 (modifiable by boons)
    public FixedPoint64 FillRate; // Multiplier (default 1.0, reduced by Heat)
    public bool BindReady;       // True when CurrentSilk >= 100
    public bool WeaverReady;     // True when CurrentSilk >= 50
}
// SilkFillSystem adds Silk on hit events
// SilkSpendSystem handles Bind and Weaver activation
```
