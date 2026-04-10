# SPORELING -- Full Class Specification

## Identity

- **Role:** Agile DPS / Assassin
- **Fantasy:** Speed and evasion. Phase through enemies. Hit-and-run.
- **Difficulty:** Intermediate
- **Ideal Fusion Role:** Pilot (movement control)

## Base Stats

| Stat | Value |
|---|---|
| HP (Masks) | 5 base / 8 max |
| Base Damage | Medium |
| Move Speed | 7.2 units/s |
| Dash Distance | 4.5 units |
| Dash Cooldown | 2.0s |
| Attack Speed | 1.4 hits/s |
| Defense Rating | Low (0.8x damage reduction) |
| Stagger Resist | Low (2 hits to stagger) |

## Signature Ability -- Spore Dash

Phase through enemies dealing 15 damage to all passed targets. 3s cooldown. Grants 0.3s intangibility during the dash animation. Dash distance: 5 units. Startup: 3 frames. Active: 8 frames. Recovery: 5 frames.

## Crests

### Swift Crest

| Property | Detail |
|---|---|
| Attack | Fast 3-hit combo (6/8/12 dmg), short range (1.5 units) |
| Dash | Double dash (two charges, 4s shared recharge) |
| Slot Layout | 3 Active / 2 Upgrade / 2 Passive |
| Playstyle | Relentless pressure, combo extensions, in-and-out aggression |

**Animation Frame Data (Swift):**

| Move | Startup | Active | Recovery | Total | Damage |
|---|---|---|---|---|---|
| Combo Hit 1 | 3f | 4f | 6f | 13f | 6 |
| Combo Hit 2 | 2f | 4f | 5f | 11f | 8 |
| Combo Hit 3 | 4f | 6f | 10f | 20f | 12 |
| Double Dash | 3f | 6f | 4f | 13f | 0 |

### Phantom Crest

| Property | Detail |
|---|---|
| Attack | Slow single strike, high backstab bonus (+80% from behind) |
| Dash | Invisibility dash (0.5s stealth after dash completes) |
| Slot Layout | 2 Active / 3 Upgrade / 2 Passive |
| Playstyle | Ambush predator, backstab specialist, hit-and-vanish |

**Animation Frame Data (Phantom):**

| Move | Startup | Active | Recovery | Total | Damage |
|---|---|---|---|---|---|
| Phase Strike | 6f | 5f | 12f | 23f | 22 |
| Backstab | 6f | 5f | 12f | 23f | 39 |
| Stealth Dash | 3f | 8f | 3f | 14f | 0 |

### Thorn Crest

| Property | Detail |
|---|---|
| Attack | Speed escalates per combo hit (+10% per successive hit, caps at +50%) |
| Dash | Poison cloud on dash (2m radius, 4 dmg/s for 3s) |
| Slot Layout | 3 Active / 3 Upgrade / 1 Passive |
| Playstyle | Ramp-up aggression, area denial through movement, sustained DPS |

**Animation Frame Data (Thorn):**

| Move | Startup | Active | Recovery | Total | Damage |
|---|---|---|---|---|---|
| Hit 1 (base) | 4f | 4f | 7f | 15f | 8 |
| Hit 5 (+50%) | 3f | 4f | 5f | 12f | 12 |
| Poison Dash | 3f | 8f | 5f | 16f | 4/s (DoT) |

## Echo Functions

| # | Function | Active Mode | Upgrade Mode | Passive Mode |
|---|---|---|---|---|
| 1 | Spore Dash | Phase-dash, 15 dmg to passed enemies, 3s CD | Added ability gains 3-unit dash before activation | +8% move speed, -0.5s global dash CD |
| 2 | Myco-Blade | 3-hit melee combo, 10/12/18 dmg | Added ability gains bleed DoT (3 dmg/s for 3s) | +5% melee damage |
| 3 | Scatter Spore | Throw 5 spore grenades in arc, 8 dmg each | Added ability spawns 2 spore mines on activation | Kills have 15% chance to drop health |
| 4 | Phase Walk | 2s invisibility + intangibility, 8s CD | Added ability grants 1s intangibility after use | -1s on all cooldowns |
| 5 | Tendril Grapple | Grapple to surface/enemy, 5 dmg on contact | Added ability pulls enemies toward player | +10% aerial damage |
| 6 | Spore Cloud | AoE smoke screen, blinds enemies 3s, 6s CD | Added ability creates smoke at impact point | Enemies have 10% miss chance against you |
| 7 | Symbiote Strike | Melee, heals 20% of damage dealt, 5s CD | Added ability heals allies in 3-tile radius for 5% | Passive regen: 1 HP per 10 seconds |
| 8 | Echo Resonance | Emit pulse: 20 dmg in 4-tile radius, 12s CD | Added ability emits weaker pulse (10 dmg, 2-tile) on use | +3% damage per active Echo Boon |

## Matchup Notes

| Vs. Class | Rating | Notes |
|---|---|---|
| Sporeling | Even | Mirror depends on Crest choice; Phantom beats Swift in neutral, Swift wins in extended trades |
| Rootwarden | Slight Advantage | Mobility lets Sporeling kite; avoid getting anchored. Backdash bait the shield bash. |
| Bloomcaster | Advantage | Close the gap with Spore Dash, Bloomcaster crumbles at melee range. Watch for retreat dash. |
| Decayer | Slight Disadvantage | Entropy Touch punishes melee approach. Avoid prolonged engagement, burst and disengage. |

## Design Notes

- Sporeling is the introductory aggressive class. Players learn spacing and dash timing.
- Swift Crest is recommended for new players. Phantom rewards advanced positioning.
- Thorn Crest bridges Sporeling into a zone-control hybrid at high skill levels.
- In Fusion, Sporeling pilots provide superior repositioning for heavier Gunner partners.
