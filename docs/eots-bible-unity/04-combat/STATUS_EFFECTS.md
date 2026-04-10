# Status Effects Specification

## The Eight Effects (One Per Civilization)

### 1. Petrify (Lithic Civilization)
| Property | Value |
|----------|-------|
| Type | Binary (on/off) |
| Duration | 2 seconds (120 ticks) |
| Effect | Target immobile, 2x damage taken |
| Stacking | Refreshes duration |
| Visual | Grey stone texture overlay, cracks on hit |
| Audio | Stone crumble SFX on apply, shatter on break |
| Counter | Can be broken by partner hitting petrified target |

### 2. Daze (Luminari Civilization)
| Property | Value |
|----------|-------|
| Type | Binary |
| Duration | 3 seconds (180 ticks) |
| Effect | 30% miss chance on target's attacks |
| Visual | Spinning stars above head, screen wobble for affected player |
| Audio | Bell chime loop, detuned |

### 3. Bloom (Verdant Civilization)
| Property | Value |
|----------|-------|
| Type | Stacking (1-5) |
| Duration | 4 seconds per stack |
| Effect | DoT: 2 damage/second per stack |
| Spread | At 3+ stacks: spreads 1 stack to enemies within 2 tiles |
| Visual | Green spores growing on target, burst at spread |
| Audio | Plant growth SFX, pop on spread |

### 4. Corrode (Ferrox Civilization)
| Property | Value |
|----------|-------|
| Type | Stacking (1-10) |
| Duration | 8 seconds per stack |
| Effect | -5% defense per stack (max -50%) |
| Visual | Orange acid dripping, armor dissolving |
| Audio | Acid hiss, metal dissolve |

### 5. Shatter (Glacine Civilization)
| Property | Value |
|----------|-------|
| Type | Stacking (1-5) |
| Duration | 6 seconds (total, not per stack) |
| Effect | At 5 stacks: AoE explosion, 200% base damage, 3-tile radius |
| Visual | Ice crystals growing on target, explosion at 5 |
| Audio | Crystal forming, dramatic shatter at 5 |
| Strategy | Pair with fast-hitting attacks to reach 5 before timer expires |

### 6. Scorch (Pyreth Civilization)
| Property | Value |
|----------|-------|
| Type | Stacking (1-10) |
| Duration | 5 seconds per stack |
| Effect | DoT: 3% base damage per stack per second |
| Visual | Fire trails, increasing flame intensity |
| Audio | Fire crackle, roar at high stacks |

### 7. Void Mark (Umbral Civilization)
| Property | Value |
|----------|-------|
| Type | Binary |
| Duration | 5 seconds (300 ticks) |
| Effect | +50% backstab damage from all sources |
| Visual | Dark purple sigil on target's back |
| Audio | Shadow whisper on apply |
| Strategy | Pair with Sporeling Phase Walk for guaranteed backstabs |

### 8. Harmonic (Resonant Civilization)
| Property | Value |
|----------|-------|
| Type | Counter (1-4) |
| Duration | Resets on 3-second gap between hits |
| Effect | At 4 hits: next hit deals 3x damage |
| Visual | Musical note VFX (quarter, half, dotted half, whole) |
| Audio | Ascending musical notes (C, E, G, C octave), chord on proc |
| Strategy | Requires sustained aggression without pause |

## Status Effect Interactions

### Duo Combinations (when both statuses active on same target)

| Status A | Status B | Interaction |
|----------|----------|------------|
| Petrify | Shatter | Petrified target counts as 2 Shatter stacks |
| Bloom | Scorch | DoT damage doubled (burning plants) |
| Corrode | Void Mark | Defense reduction applies to backstab bonus multiplicatively |
| Daze | Harmonic | Dazed target's counter doesn't decay (easier to reach 4) |
| Petrify | Corrode | Petrified target takes Corrode damage as if defense = 0 |

## ECS Implementation

```csharp
public struct StatusEffectComponent
{
    public StatusType Type;          // enum: 8 values
    public int StackCount;           // 0 = inactive
    public int TicksRemaining;       // countdown in sim ticks
    public int SourceEntityId;       // who applied it
    public FixedPoint64 Magnitude;   // effect strength
}
// Max 4 simultaneous effects per entity (one per slot)
// StatusEffectSystem processes all effects each tick
```
