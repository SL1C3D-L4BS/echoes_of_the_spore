# 05 — CLASS DESIGN, LOADOUTS & BUILD SYSTEM

## Four Base Classes

### Sporeling (Agile DPS)
- **Fantasy:** Speed and evasion. Phase through enemies. Hit-and-run.
- **Base HP:** 5 masks (can be upgraded to 8)
- **Base Damage:** Medium
- **Signature Ability:** Spore Dash (phase through enemies dealing damage)
- **Playstyle:** High mobility, low survivability, rewards aggressive positioning

### Rootwarden (Tank / Controller)
- **Fantasy:** Immovable force. Lock down space. Protect allies.
- **Base HP:** 8 masks (can be upgraded to 12)
- **Base Damage:** Low-Medium
- **Signature Ability:** Mycelial Anchor (root-zone lockdown, enemies in area cannot move)
- **Playstyle:** Slow, durable, area denial, the Pilot role's ideal class in Fusion

### Bloomcaster (Ranged Caster)
- **Fantasy:** Precise long-range damage. Zone control with projectiles.
- **Base HP:** 4 masks (fragile)
- **Base Damage:** High (per projectile)
- **Signature Ability:** Petal Storm (homing spore projectiles)
- **Playstyle:** Positional, kiting, the Gunner role's ideal class in Fusion

### Decayer (Support / Debuffer)
- **Fantasy:** Weaken everything. Make allies stronger. Embrace entropy.
- **Base HP:** 6 masks
- **Base Damage:** Low
- **Signature Ability:** Entropy Touch (weaken enemy + heal on contact)
- **Playstyle:** Team force multiplier, the Support/Relay role's ideal class in Fusion

## Crest System (3 Crests per Class = 12 Total)

Each Crest changes: basic attack animation, basic attack hitbox shape, dash behavior, and the number/type of Function slots available.

| Class | Crest | Attack Change | Dash Change | Slot Layout (A/U/P) |
|---|---|---|---|---|
| Sporeling | Swift | Fast 3-hit combo, short range | Double dash | 3/2/2 |
| Sporeling | Phantom | Slow single strike, high backstab bonus | Invisibility dash (0.5s stealth) | 2/3/2 |
| Sporeling | Frenzy | Speed escalates per combo hit | Dash resets on kill | 3/3/1 |
| Rootwarden | Sentinel | Shield bash, stun on 3rd hit | Shield dash (blocks during dash) | 2/2/3 |
| Rootwarden | Colossus | Massive overhead slam, 2x range | Ground pound dash (AoE on land) | 2/1/4 |
| Rootwarden | Thorn | Counter-stance (parry window on attack startup) | Spike dash (reflect projectiles during dash) | 2/3/2 |
| Bloomcaster | Artillery | Slow charged shot, high single-target damage | Retreat dash (fires backward while dashing) | 3/2/2 |
| Bloomcaster | Storm | Rapid-fire spread, low per-hit damage | Dash leaves lingering projectile cloud | 3/3/1 |
| Bloomcaster | Siphon | Projectiles heal 10% of damage dealt | Drain dash (touch enemies = small heal) | 2/2/3 |
| Decayer | Plague | AoE cloud attack, area denial | Plague trail (dash leaves DoT zone) | 2/3/2 |
| Decayer | Leech | Single-target drain, high lifesteal | Lunge dash (gap closer + damage) | 3/2/2 |
| Decayer | Catalyst | Attacks buff nearby allies +10% damage for 3s | Link dash (dash to ally, both gain shield) | 1/2/4 |

A = Active Function slots, U = Upgrade slots (distributed across Actives), P = Passive Function slots.

## Echo Function System (8 per Class = 32 Total)

Each Function works differently in three modes: Active, Upgrade, Passive.
Using a Function in all 3 modes unlocks its lore entry (96 lore entries total).

### Sporeling Functions (Example Set)

| Function | Active | Upgrade | Passive |
|---|---|---|---|
| Spore Dash | Phase-dash, 15 dmg to passed enemies, 3s CD | Added ability gains 3-unit dash before activation | +8% move speed, -0.5s global dash CD |
| Myco-Blade | 3-hit melee combo, 10/12/18 dmg | Added ability gains bleed DoT (3 dmg/s for 3s) | +5% melee damage |
| Scatter Spore | Throw 5 spore grenades in arc, 8 dmg each | Added ability spawns 2 spore mines on activation | Kills have 15% chance to drop health |
| Phase Walk | 2s invisibility + intangibility, 8s CD | Added ability grants 1s intangibility after use | -1s on all cooldowns |
| Tendril Grapple | Grapple to surface/enemy, 5 dmg on contact | Added ability pulls enemies toward player | +10% aerial damage |
| Spore Cloud | AoE smoke screen, blinds enemies 3s, 6s CD | Added ability creates smoke at impact point | Enemies have 10% miss chance against you |
| Symbiote Strike | Melee, heals 20% of damage dealt, 5s CD | Added ability heals allies in 3-tile radius for 5% | Passive regen: 1 HP per 10 seconds |
| Echo Resonance | Emit pulse: 20 dmg to all enemies in 4-tile radius, 12s CD | Added ability emits weaker pulse (10 dmg, 2-tile) on use | +3% damage per active Echo Boon |

## The 12 Fused Classes

| Fusion Pairing | Pilot | Gunner | Fused Name | Fusion Ability |
|---|---|---|---|---|
| Sporeling + Rootwarden | Sporeling | Rootwarden | Myco-Juggernaut | Rooted Dash: phase-charge that roots all enemies in path for 2s |
| Rootwarden + Sporeling | Rootwarden | Sporeling | Trench Spore | Anchor Burst: lockdown area then detonates spore AoE |
| Sporeling + Bloomcaster | Sporeling | Bloomcaster | Pollen Phantom | Phantom Bloom: dash leaves homing petal mines |
| Bloomcaster + Sporeling | Bloomcaster | Sporeling | Spore Sniper | Phase Petal: projectiles teleport through walls |
| Sporeling + Decayer | Sporeling | Decayer | Rot Runner | Entropy Dash: phase-run leaves decay trail draining HP |
| Decayer + Sporeling | Decayer | Sporeling | Blight Blur | Touch Phase: melee teleport chain between weakened enemies |
| Rootwarden + Bloomcaster | Rootwarden | Bloomcaster | Siege Bloom | Fortified Storm: rooted turret mode, 360° petal barrage |
| Bloomcaster + Rootwarden | Bloomcaster | Rootwarden | Bastion Caster | Root Volley: projectiles root on impact |
| Rootwarden + Decayer | Rootwarden | Decayer | Plague Fortress | Decay Anchor: lockdown zone that progressively weakens trapped enemies |
| Decayer + Rootwarden | Decayer | Rootwarden | Wilt Wall | Entropic Shield: absorbs damage, converts to AoE debuff burst |
| Bloomcaster + Decayer | Bloomcaster | Decayer | Mold Mage | Blight Bloom: homing projectiles that stack escalating debuffs |
| Decayer + Bloomcaster | Decayer | Bloomcaster | Rot Oracle | Spore Prophecy: mark enemies, marked take 3x Bloomcaster damage |

## Echo Boon System (8 Civilizations × 5 Slots × 4 Rarities)

Per-run boons offered at Reward rooms. 5 slots (Strike, Surge, Spore Cast, Symbiote, Resonance). Slots are exclusive per civilization. 4 rarities: Common, Uncommon, Rare, Heroic.

28 Duo Echoes (one per civilization pairing).
8 Legendary Echoes (one per civilization, requires 3+ boons).

See Section 24 (Data Schemas) for complete boon table.
