# Boss Roster -- All 8 Zone Bosses

## Design Template

Every boss follows a 3-phase structure with HP-threshold transitions, progressive arena changes, and escalating attack patterns. See AI_ENEMIES.md for the template overview.

## Boss 1: Spore Mother (Zone 1 -- Sporehaven)

| Phase | HP Threshold | Attacks | Arena |
|---|---|---|---|
| 1 | 100%-66% | Ground slam (3 masks, 4u AoE, 1.2s telegraph), Spawn 4 Spore Mites, Tendril swipe (2 masks, frontal cone) | Open circular arena, no hazards |
| 2 | 66%-33% | Slam creates spore clouds (2s linger, 1 mask/s), Spawns Tier 2 Tendril Creepers, Tendril swipe is faster (0.8s) | Spore clouds accumulate on floor, reducing safe space |
| 3 | 33%-0% | Continuous add spawns (2 Mites every 10s), Cloud coverage reaches 60% of arena, New: Root Cage (targets player, 3s trap, break with 5 hits) | Clouds cover most of floor. Elevated safe platforms emerge. |

**HP Pool:** 80 masks (solo) / 128 masks (4P)
**Lesson:** Add management, spatial awareness, platform navigation.
**Unique Drop:** Spore Mother's Heart (crafting material for Tier 1 boon).

## Boss 2: The Architect (Zone 2 -- Cortex)

| Phase | HP Threshold | Attacks | Arena |
|---|---|---|---|
| 1 | 100%-66% | Teleport + 5-projectile volley (1 mask each), Blueprint Beam (2 masks, narrow line, 1s charge), Clone Dash (appears at player, 2 masks) | Square arena with 4 pillars (cover) |
| 2 | 66%-33% | Input-reversal AoE zones appear (8u radius, 5s duration), Volley increased to 8 projectiles, Clone Dash leaves afterimage that fires 1 projectile | 2 pillars destroyed. Input-reversal zones spawn every 15s. |
| 3 | 33%-0% | 3 simultaneous clones (1 real, 2 shadow; shadows die in 1 hit), All attacks faster by 30%, New: Construct (builds temporary wall to trap player, 10 masks HP, 8s lifetime) | All pillars destroyed. Open arena. Constructs provide only cover. |

**HP Pool:** 90 masks (solo) / 144 masks (4P)
**Lesson:** Pattern recognition, reading fakes, maintaining orientation under input reversal.
**Unique Drop:** Architect's Lens (Codex key, unlocks Cortex lore entries).

## Boss 3: The Distiller (Zone 3 -- Ferment Pits)

| Phase | HP Threshold | Attacks | Arena |
|---|---|---|---|
| 1 | 100%-66% | Acid spray cone (2 masks, 60-degree, 6u range, 0.8s), Charge (2 masks, 8u dash, 1s telegraph), Acid glob (ranged, 1 mask + puddle 3s) | Rectangular arena, dry floor |
| 2 | 66%-33% | Arena floor floods with acid (1 mask/s contact), 3 raised platforms remain safe, Charge now leaves acid trail, Spray becomes 90-degree | Acid rising from edges. Platforms are the only safe ground. |
| 3 | 33%-0% | Only 3 small platforms remain, Distiller becomes amphibious (moves through acid freely), New: Acid Geyser (erupts under platform, 3 masks, destroys platform for 15s), Desperate Spray (360-degree, 1 mask) | Platforms crumble and regenerate. Constant repositioning required. |

**HP Pool:** 100 masks (solo) / 160 masks (4P)
**Lesson:** Platform management, jump timing, spatial pressure.
**Unique Drop:** Distiller's Flask (consumable crafting base, acid resistance).

## Boss 4: Hive Queen (Zone 4 -- Chitinhall)

| Phase | HP Threshold | Attacks | Arena |
|---|---|---|---|
| 1 | 100%-66% | Egg sac spawn (2 eggs every 15s, hatch in 5s into Chitin Knights), Claw combo (3-hit, 2/2/3 masks, 0.4s between hits), Mandible grab (unblockable, 3 masks, 1.5s escape window) | Hexagonal arena with honeycomb walls |
| 2 | 66%-33% | Swarm event every 30s (20 Spore Mites flood arena for 10s), Eggs hatch in 3s (faster), Claw combo adds 4th hit (slam, 3 masks AoE) | Honeycomb walls crack, revealing additional spawn points |
| 3 | 33%-0% | Queen becomes mobile (was stationary), movement speed 4 u/s, Eggs spawn at 2x rate, New: Royal Command (all active adds gain +50% speed for 5s), New: Brood Sacrifice (kills all adds, heals 2 masks per add killed) | Arena fully open. Queen charges across room. |

**HP Pool:** 120 masks (solo) / 192 masks (4P)
**Lesson:** Add priority, DPS race vs sustainability, combo dodging.
**Unique Drop:** Queen's Chitin (armor material, highest physical defense).

## Boss 5: The Gardener (Zone 5 -- Rot Garden)

| Phase | HP Threshold | Attacks | Arena |
|---|---|---|---|
| 1 | 100%-66% | Vine whip (2 masks, 8u ranged, 0.5s), Petal scatter (1 mask x6, random spread, 5u radius), Thorn wall (blocks path, 5s duration, 1 mask on contact) | Circular garden arena with 4 flower beds (cover) |
| 2 | 66%-33% | Garden sections bloom/decay on 10s cycle (bloomed = safe cover, decayed = poison zone), Vine whip becomes double whip (2 sequential), New: Seed Bomb (arcing, 2 masks + spawns thorns on impact) | Dynamic cover system. Must track bloom/decay cycle. |
| 3 | 33%-0% | Weeping AoE (tears are projectiles, 1 mask each, 8 per second for 3s, 12s cooldown), All attacks +20% speed, Petal scatter becomes targeted (tracks player), New: Garden's Embrace (vines grab all players, 3s escape, 2 masks/s) | All sections constantly shifting. Tragic music. Boss weeps visibly. |

**HP Pool:** 95 masks (solo) / 152 masks (4P)
**Lesson:** Environmental awareness, timing with bloom/decay cycle, emotional narrative beat.
**Unique Drop:** Gardener's Tear (Infection purge item, -10 Infection).

## Boss 6: The Smelter (Zone 6 -- Myco-Forge)

| Phase | HP Threshold | Attacks | Arena |
|---|---|---|---|
| 1 | 100%-66% | Hammer slam (3 masks, 3u AoE, 1s wind-up), Lava geyser (2 geysers, 2 masks each, marked 1s before eruption), Slag throw (2 masks, ranged, arcing) | Forge arena with conveyor belts (inactive) and anvils (cover) |
| 2 | 66%-33% | Conveyor belts activate (move players/enemies 3 u/s), Geysers increase to 4, Hammer slam creates lava pool (3s, 1 mask/s), New: Forge Breath (cone, 3 masks, 6u range, 1.5s charge) | Conveyors push players toward hazards. Anvils begin to glow (hot, 1 mask touch). |
| 3 | 33%-0% | Molten form: Smelter melts into lava, faster (5 u/s), shorter reach (melee only), leaves lava trail (3s, 1 mask/s), Hammer replaced by rapid punches (1 mask x4, 0.2s each), New: Eruption (room-wide, 3s warning, safe zone = conveyor belts only) | Arena is mostly lava. Conveyors are the only safe paths. |

**HP Pool:** 110 masks (solo) / 176 masks (4P)
**Lesson:** Conveyor mechanics, positioning discipline, phase adaptation.
**Unique Drop:** Smelter's Core (weapon upgrade material, fire damage).

## Boss 7: Veil Warden (Zone 7 -- Necroveil)

| Phase | HP Threshold | Attacks | Arena |
|---|---|---|---|
| 1 | 100%-66% | Reality clone (2 versions: 1 real, 1 shadow), Shadow deals half damage but is intangible until identified (attacking it reveals identity, 1s), Both attack with scythe slash (2 masks, 0.6s) | Chamber with mirrors. Reflections may or may not match reality. |
| 2 | 66%-33% | Arena warps: rooms within rooms (nested spaces, portals between them), Clone becomes indistinguishable for 3s after spawn, New: Void Pull (teleports player to random sub-room, 15s cooldown) | 3 interconnected sub-arenas. Boss teleports between them. |
| 3 | 33%-0% | 4 simultaneous clones (1 real, 3 shadow), All can damage you (shadows deal 1 mask, real deals 2), New: Reality Collapse (all sub-rooms merge, chaotic geometry for 10s), Scythe combos increased to 3-hit chains | Merged arena. Visual chaos. Audio cues become primary for identifying the real Warden. |

**HP Pool:** 100 masks (solo) / 160 masks (4P)
**Lesson:** Observation, audio awareness, spatial orientation under disorientation.
**Unique Drop:** Veil Shard (unlocks hidden Zone 7 lore room, Codex entries).

## Boss 8: Mycelium Heart (Zone 8 -- Necrospore Core)

| Phase | HP Threshold | Attacks | Arena |
|---|---|---|---|
| 1 | 100%-66% | Tentacle sweep (2 masks, 180-degree, 8u range, 1s), Spore barrage (1 mask x10, scattered, 3s duration), Tendril spike (3 masks, targeted, erupts from floor, 0.8s telegraph) | Organic spherical chamber. Heart is central. |
| 2 | 66%-33% | Gravity shifts (ceiling becomes floor, 5s transition, 20s duration), Tentacles attack from both surfaces, Spore barrage tracks player, New: Mycelium Wall (divides arena, forces player to one half, 10s) | Gravity flips. Players must fight on ceiling. All hazards persist. |
| 3 | 33%-0% | Mycelium speaks directly to each player (unique VO lines per class), Attacks become personalized (counters each player's loadout via AI Director), New: Absorption (attempts to fuse player into the Heart, 5s channel, party must deal 20 masks to interrupt), Tentacle speed +40% | Full AI Director control. Arena pulses with bioluminescence. Narrative climax. |

**HP Pool:** 150 masks (solo) / 240 masks (4P)
**Lesson:** Adaptation, teamwork under the AI Director's counters, narrative payoff.
**Unique Drop:** Heart Fragment (required for True Ending progression).

## Multiplayer Scaling Formula

```
Boss_HP = base_HP * (1 + 0.6 * (player_count - 1))
Boss_Damage = base_damage * (1 + 0.1 * (player_count - 1))  // capped at 1.3x
```

## Implementation Notes

- Each boss is a prefab with `BossController` (phase management), `BossAttackSet` (per-phase), and `ArenaController` (hazards)
- Phase transitions trigger `OnPhaseChange` event, consumed by arena, audio, and VFX systems
- AI Director (Boss 8 only) injects attack selection via `DirectorAttackProvider` component
- Boss HP bar uses `BossHealthUI` with phase markers and damage numbers
- All boss arenas are separate scenes loaded additively; sealed on entry
