# DECAYER -- Full Class Specification

## Identity

- **Role:** Support / Debuffer
- **Fantasy:** Weaken everything. Make allies stronger. Embrace entropy.
- **Difficulty:** Intermediate
- **Ideal Fusion Role:** Support (3P) / Relay (4P)

## Base Stats

| Stat | Value |
|---|---|
| HP (Masks) | 6 base / 9 max |
| Base Damage | Low |
| Move Speed | 5.8 units/s |
| Dash Distance | 3.5 units |
| Dash Cooldown | 2.5s |
| Attack Speed | 1.2 hits/s |
| Defense Rating | Medium (1.0x damage reduction) |
| Stagger Resist | Medium (3 hits to stagger) |
| Debuff Duration Bonus | +20% base |

## Signature Ability -- Entropy Touch

Melee-range contact: weaken target (-25% damage dealt for 4s) and heal self for 8 HP on hit. Cooldown: 6s. Startup: 5 frames. Active: 4 frames. Recovery: 8 frames. Range: 1.5 units (melee).

## Crests

### Catalyst Crest

| Property | Detail |
|---|---|
| Attack | Attacks buff nearby allies +10% damage for 3s (2-tile radius) |
| Dash | Link dash (dash to ally, both gain 10 HP shield for 3s) |
| Slot Layout | 1 Active / 2 Upgrade / 4 Passive |
| Playstyle | Pure team support, buff uptime focus, passive stat engine |

**Animation Frame Data (Catalyst):**

| Move | Startup | Active | Recovery | Total | Damage |
|---|---|---|---|---|---|
| Buff Strike | 4f | 5f | 7f | 16f | 6 (+ ally buff) |
| Link Dash | 3f | 12f | 4f | 19f | 0 (shield grant) |

### Plague Crest

| Property | Detail |
|---|---|
| Attack | AoE cloud attack, 2-tile radius, 5 dmg + 3 dmg/s DoT for 4s |
| Dash | Plague trail (dash leaves DoT zone: 4 dmg/s for 3s, 1-tile wide) |
| Slot Layout | 2 Active / 3 Upgrade / 2 Passive |
| Playstyle | DoT specialist, area denial, attrition warfare |

**Animation Frame Data (Plague):**

| Move | Startup | Active | Recovery | Total | Damage |
|---|---|---|---|---|---|
| Cloud Attack | 6f | 8f | 10f | 24f | 5 + 3/s DoT |
| Plague Dash | 3f | 8f | 5f | 16f | 4/s trail |

### Leech Crest

| Property | Detail |
|---|---|
| Attack | Single-target drain, high lifesteal (30% of damage dealt) |
| Dash | Lunge dash (gap closer, 8 dmg on contact) |
| Slot Layout | 3 Active / 2 Upgrade / 2 Passive |
| Playstyle | Self-sustaining brawler, drain tank, 1v1 duelist |

**Animation Frame Data (Leech):**

| Move | Startup | Active | Recovery | Total | Damage |
|---|---|---|---|---|---|
| Drain Strike | 5f | 5f | 8f | 18f | 12 (3.6 heal) |
| Lunge Dash | 4f | 8f | 6f | 18f | 8 |

## Echo Functions

| # | Function | Active Mode | Upgrade Mode | Passive Mode |
|---|---|---|---|---|
| 1 | Entropy Touch | Weaken target -25% dmg, heal 8 HP, 6s CD | Added ability spreads weaken to enemies in 1-tile radius | Enemies near you deal -5% damage passively |
| 2 | Rot Cloud | Deploy 3-tile toxic cloud, 6 dmg/s for 4s, 8s CD | Added ability cloud also reduces enemy healing by 50% | +10% DoT damage |
| 3 | Fungal Link | Tether to ally: share 30% of damage taken for 6s, 10s CD | Added ability tethered ally also gains +15% damage | +5% max HP |
| 4 | Decay Pulse | AoE burst: 12 dmg + remove 1 enemy buff, 3-tile radius, 9s CD | Added ability also applies 2s silence | Debuffs you apply last 15% longer |
| 5 | Spore Siphon | Drain 4 HP/s from target for 3s (12 total), 2-tile range, 7s CD | Added ability excess healing becomes shield (max 6) | Heal 1 HP when applying any debuff |
| 6 | Mycorrhizal Net | Connect 3 allies: share healing received equally for 8s, 14s CD | Added ability net also shares buff effects | Allies within 3 tiles gain +3% damage |
| 7 | Wilt | Target enemy loses 3% max HP/s for 4s, 2-tile range, 10s CD | Added ability also reduces target move speed by 30% | Enemies below 30% HP take +10% damage from all sources |
| 8 | Echo Entropy | Emit wave: all enemies in 5-tile radius lose 1 buff and gain 1 stack of Decay (5 dmg/s, stacks 3x), 16s CD | Added ability also heals allies in radius for 5 HP | +2% debuff potency per active Echo Boon |

## Matchup Notes

| Vs. Class | Rating | Notes |
|---|---|---|
| Sporeling | Slight Advantage | Entropy Touch punishes melee approaches. Plague trail denies dash-in routes. |
| Rootwarden | Even | Wilt erodes Rootwarden HP over time. Rootwarden can outlast with Fungal Regeneration. Patience wins. |
| Bloomcaster | Slight Disadvantage | Bloomcaster out-ranges Decayer. Close distance with Leech lunge or Catalyst link dash. |
| Decayer | Even | Mirror is attrition-based. Leech Crest has advantage in 1v1. Plague Crest dominates in team fights. |

## Design Notes

- Decayer is the team force multiplier. Low solo damage, massive team impact.
- Catalyst Crest is the purest support build (1 Active slot, 4 Passive slots for stat stacking).
- Plague Crest transitions Decayer into a solo-viable DoT specialist.
- Leech Crest is the "selfish support" option: sustain through offense, viable in solo runs.
- In Fusion, Decayer as Support/Relay provides constant debuff pressure on enemies the Pilot engages.
