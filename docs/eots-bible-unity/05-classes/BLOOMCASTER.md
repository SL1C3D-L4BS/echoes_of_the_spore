# BLOOMCASTER -- Full Class Specification

## Identity

- **Role:** Ranged Caster / Glass Cannon
- **Fantasy:** Precise long-range damage. Zone control with projectiles.
- **Difficulty:** Advanced
- **Ideal Fusion Role:** Gunner (aim and damage output)

## Base Stats

| Stat | Value |
|---|---|
| HP (Masks) | 4 base / 6 max (fragile) |
| Base Damage | High |
| Move Speed | 6.0 units/s |
| Dash Distance | 4.0 units |
| Dash Cooldown | 2.5s |
| Attack Speed | 1.0 hits/s (charge-dependent) |
| Defense Rating | Very Low (0.6x damage reduction) |
| Stagger Resist | Very Low (1 hit to stagger) |
| Projectile Range | 12 units |

## Signature Ability -- Petal Storm

Launch 6 homing spore projectiles that seek the nearest enemy. Each deals 7 damage (42 total if all connect). Projectiles have moderate tracking (45 deg/s turn rate). Cooldown: 8s. Startup: 6 frames. Projectile lifetime: 3s.

## Crests

### Artillery Crest

| Property | Detail |
|---|---|
| Attack | Slow charged shot: 0.8s charge for 28 dmg, 14 units range |
| Dash | Retreat dash (fires a 12 dmg projectile backward while dashing away) |
| Slot Layout | 3 Active / 2 Upgrade / 2 Passive |
| Playstyle | Sniper, long-range burst, positional discipline |

**Animation Frame Data (Artillery):**

| Move | Startup | Active | Recovery | Total | Damage |
|---|---|---|---|---|---|
| Charged Shot (full) | 48f (charge) | 3f | 8f | 59f | 28 |
| Charged Shot (tap) | 0f | 3f | 8f | 11f | 8 |
| Retreat Dash | 3f | 8f | 5f | 16f | 12 (backward) |

### Bloom Crest

| Property | Detail |
|---|---|
| Attack | Rapid-fire spread, 3 projectiles per shot, 4 dmg each |
| Dash | Dash leaves lingering projectile cloud (6 dmg/s for 2s, 2-tile radius) |
| Slot Layout | 3 Active / 3 Upgrade / 1 Passive |
| Playstyle | Area denial, crowd control, mid-range zoner |

**Animation Frame Data (Bloom):**

| Move | Startup | Active | Recovery | Total | Damage |
|---|---|---|---|---|---|
| Spread Shot | 4f | 3f | 6f | 13f | 4 x3 |
| Cloud Dash | 3f | 8f | 4f | 15f | 6/s (zone) |

### Weaver Crest

| Property | Detail |
|---|---|
| Attack | Projectiles heal allies for 10% of damage dealt on pass-through |
| Dash | Drain dash (touching enemies during dash heals 5 HP) |
| Slot Layout | 2 Active / 2 Upgrade / 3 Passive |
| Playstyle | Support-caster hybrid, sustain through offense, team healer |

**Animation Frame Data (Weaver):**

| Move | Startup | Active | Recovery | Total | Damage |
|---|---|---|---|---|---|
| Healing Shot | 5f | 3f | 7f | 15f | 14 (+ 1.4 heal to allies) |
| Drain Dash | 3f | 10f | 5f | 18f | 0 (5 HP self-heal) |

## Echo Functions

| # | Function | Active Mode | Upgrade Mode | Passive Mode |
|---|---|---|---|---|
| 1 | Petal Storm | 6 homing projectiles, 7 dmg each, 8s CD | Added ability gains 2 extra projectiles | +10% projectile speed |
| 2 | Thorn Volley | Fire 3 piercing thorns in a line, 12 dmg each, 5s CD | Added ability thorns bounce once off walls | +5% ranged damage |
| 3 | Spore Mine | Place proximity mine, 20 dmg on trigger, max 3 active, 4s CD per mine | Added ability mines also slow (40% for 2s) | Mine placement range +3 tiles |
| 4 | Bloom Barrier | Create petal shield absorbing 15 dmg, 3s duration, 10s CD | Added ability reflects 30% of absorbed dmg | Gain 2 HP shield every 15s |
| 5 | Pollen Burst | Targeted AoE: 3-tile radius, 14 dmg, 2s blind, 7s CD | Added ability leaves pollen zone (3 dmg/s for 3s) | +8% AoE radius on all abilities |
| 6 | Vine Snare | Root 1 enemy for 2s at 8-tile range, 6 dmg, 6s CD | Added ability chains to 1 additional enemy | Rooted enemies take +10% damage from you |
| 7 | Photosynthesis | Channel: heal 2 HP/s while standing in light zones, no CD | Added ability creates a small light zone on use (2-tile, 5s) | +1 HP on entering any new room |
| 8 | Echo Cascade | Fire spiral of 12 petals outward, 6 dmg each, 14s CD | Added ability fires a second spiral 0.5s later | +3% projectile damage per active Echo Boon |

## Matchup Notes

| Vs. Class | Rating | Notes |
|---|---|---|
| Sporeling | Disadvantage | Sporeling closes distance easily. Retreat dash is critical. Place mines at predicted dash endpoints. |
| Rootwarden | Slight Disadvantage | Rootwarden absorbs poke and pulls with Thorn Whip. Maintain max range. Artillery Crest preferred. |
| Bloomcaster | Even | Mirror depends on aim. Artillery wins at max range, Bloom wins in mid-range trades. |
| Decayer | Slight Advantage | Out-range Decayer's touch abilities. Bloom Crest zone denial keeps Decayer at bay. |

## Design Notes

- Bloomcaster is the highest skill-ceiling class. Demands precise aim and constant repositioning.
- Artillery Crest is the purest expression of the glass cannon archetype.
- Bloom Crest transitions Bloomcaster into a mid-range zoner, more forgiving but lower burst.
- Weaver Crest is the support variant; ideal for co-op when no Decayer is present.
- Lowest HP in the game. Two mistakes and you are dead. Rewards mastery.
