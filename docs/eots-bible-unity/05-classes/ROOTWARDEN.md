# ROOTWARDEN -- Full Class Specification

## Identity

- **Role:** Tank / Controller
- **Fantasy:** Immovable force. Lock down space. Protect allies.
- **Difficulty:** Beginner-friendly
- **Ideal Fusion Role:** Pilot (anchored movement, area lockdown)

## Base Stats

| Stat | Value |
|---|---|
| HP (Masks) | 8 base / 12 max |
| Base Damage | Low-Medium |
| Move Speed | 5.0 units/s |
| Dash Distance | 3.0 units |
| Dash Cooldown | 3.0s |
| Attack Speed | 0.9 hits/s |
| Defense Rating | High (1.4x damage reduction) |
| Stagger Resist | Very High (5 hits to stagger) |

## Signature Ability -- Mycelial Anchor

Deploy a root-zone lockdown in a 3-tile radius. Enemies inside cannot move for 2.5s. Anchor persists for 4s total (movement lock decays to slow after 2.5s). Cooldown: 10s. Startup: 8 frames. Active: duration-based. Recovery: 6 frames.

## Crests

### Sentinel Crest

| Property | Detail |
|---|---|
| Attack | Shield bash, stun on 3rd hit (0.8s stun) |
| Dash | Shield dash (blocks frontal damage during dash) |
| Slot Layout | 2 Active / 2 Upgrade / 3 Passive |
| Playstyle | Frontline brawler, stun-lock combos, ally protection |

**Animation Frame Data (Sentinel):**

| Move | Startup | Active | Recovery | Total | Damage |
|---|---|---|---|---|---|
| Bash Hit 1 | 5f | 6f | 8f | 19f | 8 |
| Bash Hit 2 | 4f | 6f | 7f | 17f | 8 |
| Bash Hit 3 (stun) | 6f | 8f | 12f | 26f | 14 |
| Shield Dash | 4f | 10f | 6f | 20f | 5 |

### Bulwark Crest

| Property | Detail |
|---|---|
| Attack | Counter stance: parry window on attack startup (frames 1-4) |
| Dash | Spike dash (reflects projectiles during dash) |
| Slot Layout | 2 Active / 3 Upgrade / 2 Passive |
| Playstyle | Reactive defender, punish-based, anti-ranged specialist |

**Animation Frame Data (Bulwark):**

| Move | Startup (Parry) | Active | Recovery | Total | Damage |
|---|---|---|---|---|---|
| Counter Stance | 4f (parry) | 6f | 10f | 20f | 18 (on parry success) |
| Failed Parry | 4f | 0f | 14f | 18f | 0 |
| Spike Dash | 5f | 10f | 8f | 23f | 8 + reflect |

### Bramble Crest

| Property | Detail |
|---|---|
| Attack | Massive overhead slam, 2x range (3.0 units) |
| Dash | Ground pound dash (AoE 2-tile radius on landing, 10 dmg) |
| Slot Layout | 2 Active / 1 Upgrade / 4 Passive |
| Playstyle | Area control, slow but devastating, passive stat monster |

**Animation Frame Data (Bramble):**

| Move | Startup | Active | Recovery | Total | Damage |
|---|---|---|---|---|---|
| Overhead Slam | 10f | 8f | 14f | 32f | 22 |
| Ground Pound Dash | 5f | 12f | 10f | 27f | 10 (AoE) |

## Echo Functions

| # | Function | Active Mode | Upgrade Mode | Passive Mode |
|---|---|---|---|---|
| 1 | Mycelial Anchor | Root-zone lockdown, 3-tile radius, 2.5s root, 10s CD | Added ability gains 1-tile radius expansion | +15% slow aura to nearby enemies (2-tile) |
| 2 | Bark Armor | Grant self 4-hit shield absorb, 8s CD | Added ability extends shield to 1 nearby ally | +2 max HP |
| 3 | Root Slam | Ground pound: 18 dmg in 2-tile cone, 6s CD | Added ability creates 2s tremor zone (4 dmg/s) | +10% knockback on all attacks |
| 4 | Spore Wall | Summon fungal barrier (3-tile wide, 5s duration, 8s CD) | Added ability causes wall to release spores on break (8 dmg AoE) | Block 1 projectile every 12s automatically |
| 5 | Thorn Whip | 3-tile range pull, 8 dmg, brings enemy to melee range, 7s CD | Added ability applies 2s slow after pull | +1.5 tile melee range |
| 6 | Fungal Regeneration | Heal 3 HP over 5s, rooted in place during heal, 12s CD | Added ability heals allies in 2-tile radius for 50% amount | Passive regen: 1 HP per 8 seconds |
| 7 | Tremor Step | Next 3 steps create shockwaves (5 dmg each, 1-tile radius), 10s CD | Added ability increases shockwave radius to 2 tiles | +12% damage while standing still |
| 8 | Echo Bastion | Emit defensive pulse: allies in 4-tile radius gain 30% DR for 4s, 15s CD | Added ability also cleanses 1 debuff from allies | +5% DR per active Echo Boon |

## Matchup Notes

| Vs. Class | Rating | Notes |
|---|---|---|
| Sporeling | Slight Disadvantage | Sporeling kites effectively. Land Anchor to lock down, then punish. Sentinel stun is key. |
| Rootwarden | Even | Mirror is slow. Bramble Crest wins raw damage trades. Bulwark parry timing is decisive. |
| Bloomcaster | Advantage | High HP absorbs poke. Close distance with Thorn Whip. Bulwark reflects projectiles. |
| Decayer | Even | Entropy Touch erodes HP over time, but Rootwarden can outlast with Fungal Regeneration. |

## Design Notes

- Rootwarden is the anchor of any team composition. Highest survivability, lowest mobility.
- Sentinel is the default pick for co-op play (stun enables teammate follow-up).
- Bulwark shines against Bloomcasters and projectile-heavy bosses.
- Bramble maximizes solo viability with passive stat stacking and raw slam damage.
- In Fusion, Rootwarden pilots ensure the fused entity controls space rather than chasing.
