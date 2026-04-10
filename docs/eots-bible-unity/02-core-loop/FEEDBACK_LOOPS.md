# Feedback Loop Architecture

## Positive Feedback Loops (Intentional)

### 1. Combat Momentum Loop
```
Hit enemy → Silk Gauge fills (+5%)
  → Full gauge → Choose: Heal or Weaver Technique
  → Heal → Survive longer → Hit more enemies → More Silk
  → Weaver → More damage → Kill faster → More Silk
  → Both paths reward aggression
```
**Brake:** Taking damage resets combo counter, Silk gain rate unchanged

### 2. Infection Power Loop
```
Kill enemies → Gain Infection (+1-3 per kill)
  → Higher Infection → Higher damage multiplier
  → Higher damage → Kill faster → More Infection
  → More Infection → Voice corrupts (comedy)
  → Voice corruption → Shareable clips → Social validation
```
**Brake:** Purification costs Echoes, high Infection = voice unusable for coordination

### 3. Community Tree Snowball
```
Server grows → More content unlocked
  → More content → More reasons to play
  → More players → Faster tree growth
  → Faster growth → More engagement
```
**Brake:** Exponential cost formula (1.8^depth) prevents instant completion

### 4. Knowledge Accumulation
```
Try boon combo → Discover synergy
  → Synergy enables deeper runs → More boons available
  → More boons → More combos to discover
  → Codex tracks discoveries → Completionist drive
```
**No brake needed** — this is the long-term retention engine

## Negative Feedback Loops (Balancing)

### 1. Infection Tradeoff
```
High Infection → Powerful stats
  BUT → Voice unusable → Coordination breaks
  AND → Purification costs Echoes → Less Community Tree contribution
  → Must balance power vs communication vs community
```

### 2. Heat Difficulty Scaling
```
High Heat → More Echoes per run
  BUT → Higher failure rate → Net Echoes may decrease
  → Optimal Heat is skill-dependent, not max Heat
```

### 3. Rubber-Banding (Difficulty)
```
Struggling → Boon quality increases
  → Better boons → Better chance of survival
  → Dominating → No adjustment (don't punish skill)
```

## Feedback Timing

| Feedback Type | Delay | Example |
|--------------|-------|---------|
| **Immediate** | < 1 frame | Hitstop, screen shake, SFX |
| **Short-term** | 1-5 seconds | Damage numbers, combo counter, Silk fill |
| **Medium-term** | 1-5 minutes | Boon selection, room clear reward |
| **Long-term** | 10-40 minutes | Run completion, Echo deposit, Community Tree |
| **Persistent** | Days-weeks | Codex progress, Heat milestones, server evolution |

## Juiciness Checklist (Every Combat Action)

Every attack must have ALL of these:
- [ ] Visual: screen shake (intensity scales with damage)
- [ ] Visual: hitstop (3 frames on enemy hit, 5 on player hit)
- [ ] Visual: knockback (distance scales with attack type)
- [ ] Visual: hit VFX (sparks, particles)
- [ ] Visual: damage number (color-coded)
- [ ] Audio: hit SFX (pitch increases with combo)
- [ ] Audio: impact layer (bass thud)
- [ ] Haptic: controller rumble (duration scales with damage)
- [ ] State: Silk Gauge increment
- [ ] State: Infection increment
- [ ] State: Combo counter increment
