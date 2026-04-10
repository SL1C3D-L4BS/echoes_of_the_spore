# CREST SYSTEM -- Design Specification

## Overview

Each of the 4 base classes has 3 Crests, totaling 12 Crests in the game. A Crest fundamentally changes how a class plays by modifying four systems simultaneously: basic attack animation/hitbox, dash behavior, and Function slot distribution across Active/Upgrade/Passive.

Players select a Crest at the start of each run. Crests cannot be swapped mid-run.

## Crest Modification Matrix

| Class | Crest | Attack Change | Dash Change | Slot Layout (A/U/P) |
|---|---|---|---|---|
| Sporeling | Swift | Fast 3-hit combo, short range | Double dash (2 charges) | 3/2/2 |
| Sporeling | Phantom | Slow single strike, +80% backstab bonus | Invisibility dash (0.5s stealth) | 2/3/2 |
| Sporeling | Thorn | Speed escalates per combo hit (+10%/hit, cap +50%) | Poison cloud on dash (4 dmg/s, 3s) | 3/3/1 |
| Rootwarden | Sentinel | Shield bash, stun on 3rd hit (0.8s) | Shield dash (blocks frontal damage) | 2/2/3 |
| Rootwarden | Bulwark | Counter stance (parry window frames 1-4) | Spike dash (reflects projectiles) | 2/3/2 |
| Rootwarden | Bramble | Massive overhead slam, 2x range | Ground pound dash (AoE on land) | 2/1/4 |
| Bloomcaster | Artillery | Slow charged shot (0.8s charge, 28 dmg) | Retreat dash (fires backward) | 3/2/2 |
| Bloomcaster | Bloom | Rapid-fire spread (3 proj, 4 dmg each) | Lingering projectile cloud on dash | 3/3/1 |
| Bloomcaster | Weaver | Projectiles heal allies for 10% of damage | Drain dash (touch = 5 HP heal) | 2/2/3 |
| Decayer | Catalyst | Attacks buff allies +10% dmg for 3s | Link dash (dash to ally, both shield) | 1/2/4 |
| Decayer | Plague | AoE cloud, area denial + DoT | Plague trail (DoT zone behind dash) | 2/3/2 |
| Decayer | Leech | Single-target drain, 30% lifesteal | Lunge dash (gap closer + 8 dmg) | 3/2/2 |

## Slot Distribution Design Philosophy

Total slots per Crest always equal 7. The distribution defines the build archetype:

| Distribution | Archetype | Example |
|---|---|---|
| 3+ Active | Ability-spam, high burst | Swift Sporeling, Artillery Bloomcaster |
| 3+ Upgrade | Combo-focused, ability synergy | Phantom Sporeling, Plague Decayer |
| 3+ Passive | Stat-based, consistency | Bramble Rootwarden, Catalyst Decayer |

### Slot Type Details

- **Active (A):** Equip a Function in its Active mode. Provides an activatable ability with a cooldown.
- **Upgrade (U):** Attach a Function to an existing Active. Adds secondary effects to that ability.
- **Passive (P):** Equip a Function in its Passive mode. Provides a permanent stat bonus or triggered effect.

## Unlock Conditions

| Crest Position | Unlock Requirement |
|---|---|
| Crest 1 (default) | Available from start |
| Crest 2 | Complete 1 full run with that class (any ending) |
| Crest 3 | Defeat the class-specific Lore Boss |

Default Crests per class:
- Sporeling: Swift
- Rootwarden: Sentinel
- Bloomcaster: Artillery
- Decayer: Plague

## Crest Interactions with Fusion

When two players Fuse, the Pilot's Crest determines dash behavior and movement. The Gunner's Crest determines attack animations and hitboxes. Slot layouts merge (sum both, cap at 10 total).

| Fusion Role | Contributes |
|---|---|
| Pilot | Dash behavior, move speed, passive aura |
| Gunner | Attack animation, hitbox, projectile properties |
| Support (3P) | Adds 1 Passive slot to fused entity |
| Relay (4P) | Adds 1 Upgrade slot to fused entity |

## Balance Philosophy

- No Crest should be strictly dominant. Each should have at least one matchup where it is preferred.
- Slot distribution is the primary balance lever. More Actives = more burst but less consistency.
- Crests with higher Passive counts are balanced by weaker active abilities.
- Dash modifications are balanced around equivalent total utility (damage, defense, mobility).
- Crest-specific balance changes are preferred over base-class changes during tuning.

## Crest Archetype Roles

| Archetype | Crests | Shared Trait |
|---|---|---|
| Aggressive | Swift, Leech, Artillery | 3+ Active slots, burst damage |
| Defensive | Sentinel, Bulwark, Weaver | Defensive dash, 3+ Passive or Upgrade |
| Specialist | Phantom, Thorn, Bloom, Plague | Unique mechanic (stealth, ramp, zone, DoT) |
| Support | Catalyst, Bramble, Weaver | Team utility, passive stat stacking |

## Data Schema Reference

Each Crest entry in the data files contains:
```
crest_id: string
class_id: string
attack_anim: AnimationClip reference
attack_hitbox: HitboxData reference
dash_override: DashBehavior reference
slot_layout: { active: int, upgrade: int, passive: int }
unlock_condition: UnlockCondition reference
```
