# Reward Cadence

## Reward Frequency per Run

Target: meaningful reward every **2-3 minutes** of gameplay.

```
Minute 0    ─── Hub (loadout is the "pre-reward")
Minute 2    ─── First kill streak → Silk gauge fills
Minute 4    ─── Room clear → Infection +2-5
Minute 5    ─── REWARD ROOM: Echo Boon #1
Minute 8    ─── Rare enemy drop (material)
Minute 10   ─── Rest room (Silk refill = safety reward)
Minute 12   ─── REWARD ROOM: Echo Boon #2
Minute 15   ─── Fusion Node (mechanical reward: new abilities)
Minute 18   ─── Hidden room discovery (bonus Echoes)
Minute 20   ─── REWARD ROOM: Echo Boon #3
Minute 25   ─── Boss loot drop
Minute 30   ─── Community Tree contribution (social reward)
Minute 32   ─── Codex entry unlock (completion reward)
```

## Reward Types

| Type | Frequency | Persistence | Dopamine Profile |
|------|-----------|-------------|-----------------|
| **Echo Boon** | 3 per run | Per-run only | High (choice + power spike) |
| **Silk Gauge fill** | Continuous | Per-room | Low (steady drip) |
| **Infection Level** | Continuous | Per-run, carryable | Medium (risk/reward tension) |
| **Echo currency** | Per kill + boss | Permanent | Medium (accumulation) |
| **Codex entry** | Per unique action | Permanent | High (discovery) |
| **Community Tree node** | Per server milestone | Permanent + shared | Very high (social validation) |
| **Cosmetic unlock** | Per Heat/Codex milestone | Permanent | Very high (identity) |
| **Fossil Mural** | Per zone, first visit | Permanent | High (lore discovery) |

## Variable Ratio Reinforcement

### Boon Rarity Distribution (per offering)

| Rarity | Probability | Power Level | Visual |
|--------|------------|-------------|--------|
| Common | 50% | 1.0x base | White glow |
| Uncommon | 30% | 1.5x base | Green glow |
| Rare | 15% | 2.5x base | Blue glow |
| Heroic | 5% | 4.0x base | Gold glow + particle effect |

### Duo Echo Chance
When player has boons from 2+ civilizations, Reward Room has 20% chance to offer a Duo Echo (civilization pairing). Visual: both civilization colors swirling.

### Legendary Echo Chance
When player has 3+ boons from same civilization, 10% chance for Legendary. Visual: civilization sigil with golden aura, screen pulse.

## Reward Pacing Rules

1. **Never go more than 3 rooms without a reward** (enemy loot, hidden room, or Reward Room)
2. **Boon offerings get better after hard rooms** — if player took damage in 3+ consecutive rooms, next boon offering adds +1 rarity tier
3. **First boon of a run is always impactful** — minimum Uncommon in slot 1
4. **Boss loot scales with Heat** — Heat 0: 100 Echoes, Heat 50: 350 Echoes
5. **Community Tree progress is always visible** — progress bar in HUD corner, pulses on contribution
