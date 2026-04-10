# Damage Model Specification

## Formula

```
FinalDamage = BaseDamage × ClassMult × BoonMult × InfectionMult × CritMult × DefenseReduction × HeatMult
```

All values are **FixedPoint64**. No floating-point at any stage.

## Component Values

### BaseDamage (per attack type)

| Attack | Sporeling | Rootwarden | Bloomcaster | Decayer |
|--------|-----------|------------|-------------|---------|
| Light 1 | 8 | 10 | 6 | 5 |
| Light 2 | 8 | 10 | 6 | 5 |
| Light 3 | 12 | 15 | 10 | 8 |
| Heavy | 20 | 25 | 18 | 12 |
| Dash Attack | 15 | 12 | 10 | 8 |
| Air Light | 8 | 8 | 6 | 5 |
| Air Heavy | 18 | 20 | 15 | 10 |

### ClassMultiplier

| Class | Multiplier | Notes |
|-------|-----------|-------|
| Sporeling | 1.0 FP64 | Baseline |
| Rootwarden | 0.85 FP64 | Lower damage, higher HP |
| Bloomcaster | 1.2 FP64 | Glass cannon |
| Decayer | 0.7 FP64 | Debuff-focused, not damage |

### InfectionMultiplier

| Infection Tier | Multiplier |
|---------------|-----------|
| 0-10 | 1.0 |
| 11-25 | 1.05 |
| 26-50 | 1.15 |
| 51-75 | 1.30 |
| 76-90 | 1.50 |
| 91-100 | 1.75 |

### CritMultiplier

| Condition | Multiplier | Trigger |
|-----------|-----------|---------|
| Normal hit | 1.0 | Default |
| Critical hit | 1.5 | 5% base chance + boon modifiers |
| Backstab | 1.3 | Hit from behind (>135° arc) |
| Void Mark backstab | 1.8 | Backstab + Void Mark status (1.3 × 1.5 - 0.05) |

### DefenseReduction

```
DefenseReduction = max(0.1, 1.0 - (EnemyDefense × 0.01))
```

| Enemy Tier | Defense | Reduction |
|-----------|---------|-----------|
| Tier 1 | 0-5 | 0.95-1.0 |
| Tier 2 | 10-20 | 0.80-0.90 |
| Tier 3 | 20-35 | 0.65-0.80 |
| Boss | 25-40 | 0.60-0.75 |

### HeatMultiplier (enemy damage to player)

```
HeatDamageMult = 1.0 + (Heat × 0.02)
```

Heat 0 = 1.0x, Heat 25 = 1.5x, Heat 50 = 2.0x

## Damage Number Display

| Type | Color | Font Size | Animation |
|------|-------|-----------|-----------|
| Normal | White (#E8E4D9) | 14pt | Float up, fade |
| Critical | Cyan (#00FFD1) | 18pt, bold | Float up, scale pulse |
| Duo Echo | Gold (#C9A84C) | 20pt, bold | Float up, sparkle |
| Status tick | Status color | 10pt | Float up, no scale |
| Heal | Green (#2ECC71) | 16pt | Float up |
| Enemy damage | Red (#E74C3C) | 16pt | Shake, float down |

## Implementation Notes

```csharp
// All damage math in DamageSystem.cs (Core/Systems/)
// Uses FixedPoint64 exclusively
public struct DamageResult
{
    public int FinalDamage;  // Integer output after all multipliers
    public bool IsCrit;
    public bool IsBackstab;
    public DamageType Type;
}
```
