# Infection & Combat Interactions

## Overview

Infection (0-100) is a corruption meter that accumulates during delves. Higher Infection grants raw power but distorts the player's experience. Combat is the primary domain where Infection manifests mechanically, visually, and audibly.

## Infection Tiers

| Tier | Range | Name | Color Code |
|------|-------|------|-----------|
| 0 | 0-10 | Clean | White (#E8E4D9) |
| 1 | 11-25 | Touched | Pale green (#A8D5A2) |
| 2 | 26-50 | Tainted | Yellow-green (#C4D946) |
| 3 | 51-75 | Corrupted | Orange (#E8943A) |
| 4 | 76-90 | Consumed | Red (#D94040) |
| 5 | 91-100 | Mycelial | Deep purple (#6B2D8B) |

## Combat Stat Multipliers by Tier

| Stat | Tier 0 | Tier 1 | Tier 2 | Tier 3 | Tier 4 | Tier 5 |
|------|--------|--------|--------|--------|--------|--------|
| Damage dealt | 1.00x | 1.05x | 1.15x | 1.30x | 1.50x | 1.75x |
| Damage taken | 1.00x | 1.00x | 1.05x | 1.15x | 1.30x | 1.50x |
| Crit chance | 5% | 5% | 7% | 10% | 15% | 20% |
| Crit multiplier | 1.50x | 1.50x | 1.50x | 1.60x | 1.75x | 2.00x |
| Attack speed | 1.00x | 1.00x | 1.02x | 1.05x | 1.08x | 1.10x |
| Silk fill rate | 1.00x | 1.00x | 0.95x | 0.85x | 0.75x | 0.60x |
| Bind heal HP | 3 | 3 | 3 | 3 | 2 | 2 |
| Weaver cost | 50 | 50 | 50 | 50 | 65 | 65 |

The tradeoff is explicit: higher Infection = more damage output but more damage taken, less healing, and more expensive abilities.

## Infection Accumulation Sources

| Source | Infection Gain | Notes |
|--------|---------------|-------|
| Room clear (base) | +2 per room | Unavoidable baseline |
| Taking damage | +1 per hit received | Punishment for getting hit |
| Combo bonus | +0.5% per 10 combo hits | Aggressive play adds Infection |
| Infected enemy kill | +3 per kill | Specific enemy type |
| Infected hazard contact | +5 per tick | Environmental zones |
| Boss kill | +5 per boss | Major encounters add corruption |
| Heat: Necrotic Air (per rank) | +1 additional per room | Heat condition amplification |

## Infection Reduction (Purification)

| Method | Infection Removed | Cost | Location |
|--------|------------------|------|----------|
| Rest room purify | 10 points | 50 Echoes base | Rest rooms |
| Full purification | All (reset to 0) | Scaling cost (see formula) | Canopy (between runs) |
| Purification Boon | 15 points | Echo Boon slot | During run |

### Purification Cost Formula

```
full_purify_cost = floor(current_infection × current_infection × 0.5)
```

| Current Infection | Full Purify Cost (Echoes) |
|------------------|-------------------------|
| 10 | 50 |
| 25 | 312 |
| 50 | 1,250 |
| 75 | 2,812 |
| 100 | 5,000 |

## Visual Changes by Tier

| Tier | Character Model | Particle Effects | Screen Effects |
|------|----------------|-----------------|----------------|
| 0 (Clean) | Normal appearance | None | None |
| 1 (Touched) | Faint vein lines on arms | Occasional green spore | None |
| 2 (Tainted) | Veins visible on torso/limbs | Constant spore trail | Subtle edge vignette (green) |
| 3 (Corrupted) | Partial skin texture change (fungal) | Dense spore cloud | Vignette deepens, minor chromatic aberration |
| 4 (Consumed) | Major model alteration (growths) | Heavy particle emission | Strong vignette, screen pulse on heartbeat |
| 5 (Mycelial) | Near-complete transformation | Full aura, ground corruption | Heavy distortion, intermittent flash |

## Audio Changes by Tier

| Tier | Voice Processing | Hit Sounds | Ambient |
|------|-----------------|------------|---------|
| 0 | Clean | Normal | None |
| 1 | Subtle reverb (+5%) | Normal | Faint mycelium hum |
| 2 | Reverb (+15%), slight pitch shift (-2%) | Wet layer added | Low drone |
| 3 | Heavy reverb (+30%), pitch shift (-5%), chorus | Squelch on contact | Pulsing drone, whispers |
| 4 | Distorted, formant shift, delay | Organic crunch | Constant whispers, heartbeat |
| 5 | Full DSP corruption, Mycelium voice bleeds in | Alien resonance | Narrator speaks unbidden |

## Gameplay Impact by Tier

| Tier | Gameplay Change |
|------|----------------|
| 0 | Standard play |
| 1 | No mechanical change (visual-only foreshadowing) |
| 2 | Bind heal animation +0.1s; Narrator comments on corruption |
| 3 | Bind heal -1 HP; Weaver Technique gains corruption VFX variant |
| 4 | Weaver cost +15 Silk (from 50 to 65); all attacks gain AoE splash (0.5 tile radius) |
| 5 | All attacks +75% damage; Silk fill -40%; Bind heals only 2 HP; Narrator actively distracts |

## Infection and Status Effects

| Status Effect | Interaction at Tier 3+ |
|--------------|----------------------|
| Bloom | Player's Bloom DoT spreads to allies within 1 tile (friendly fire) |
| Corrode | Self-applied Corrode (-5% defense) at Tier 4+ on taking damage |
| Void Mark | Player radiates Void Mark to nearby enemies passively at Tier 5 |
| Scorch | Scorch DoT on player ticks 20% faster at Tier 3+ |
| All others | No Infection-specific interaction |

## Infection and Fusion

| Scenario | Behavior |
|----------|----------|
| Both players same tier | Fused entity uses that tier |
| Players different tiers | Fused entity uses higher tier |
| Infection gain while fused | Split equally between both players on unmerge |
| Purification while fused | Not available; must unmerge first |

## Codex Tracking

| Entry | Unlock |
|-------|--------|
| "First Contact" | Reach Tier 1 for the first time |
| "Embracing the Rot" | Reach Tier 5 in a single run |
| "Purity Run" | Complete all 8 zones at Tier 0 |
| "Corrupted Chorus" | Trigger all 6 DSP corruption effects in one run |
| "Symbiotic Overload" | Kill a boss while both players are at Tier 4+ |

## ECS Implementation

```csharp
public struct InfectionComponent
{
    public int CurrentInfection;       // 0-100
    public InfectionTier CurrentTier;  // enum: Clean through Mycelial
    public FixedPoint64 DamageDealtMult;
    public FixedPoint64 DamageTakenMult;
    public FixedPoint64 CritChance;
    public FixedPoint64 SilkFillMult;
}
// InfectionSystem recalculates multipliers on infection change
// InfectionVFXSystem updates visuals per tier
// InfectionAudioSystem applies DSP chain per tier
```
