# 08 — AI, ENEMY TAXONOMY, ECOSYSTEM SIMULATION & BOSS LOGIC

## Enemy Tier System

### Tier 1: Sporelings (Fodder)
- HP: 2-5 masks
- Damage: 1 mask per hit
- Behavior: simple patrol → chase on sight → attack in range
- AI: finite state machine (Idle → Alert → Chase → Attack → Flee)
- Purpose: fill rooms, build combo counter, provide Silk/Infection, food for Tier 2
- Count per room: 5-15
- Examples: Puffcap (stationary, explodes on proximity), Tendril Creeper (wall-crawling, lunges), Spore Mite (swarm AI, moves in groups of 5-8)

### Tier 2: Predators (Elites)
- HP: 8-15 masks
- Damage: 2 masks per hit
- Behavior: patrol → hunt Tier 1 → attack player on sight → retreat at low HP
- AI: behavior tree with utility scoring (targets based on proximity + threat + hunger)
- Purpose: medium threat, teach attack patterns, drop uncommon loot
- Count per room: 1-3
- Examples: Chitin Knight (sword + shield, parryable), Bloom Sniper (ranged, retreats if approached), Rot Hulk (slow, heavy, charges), Myco-Lurker (ambush from walls, stealth)

### Tier 3: Apex (Mini-bosses)
- HP: 25-50 masks
- Damage: 2-3 masks per hit
- Behavior: territorial, hunts Tier 2, actively pursues player, does not flee
- AI: behavior tree with phase transitions (HP thresholds trigger new attack patterns)
- Purpose: room-defining encounter, rare spawn (5% per hour of room being unvisited), drops rare loot
- Count per room: 0-1
- Examples: Hive Matriarch (summons Tier 1 swarms), The Distillery (acid AoE + regeneration), Vein Warden (blocks paths, must be killed to proceed)

### Mycelium Hunters (Zone 7 Exclusive)
- HP: Effectively infinite (no practical way to kill in normal gameplay)
- Damage: Instant kill on contact
- Behavior: patrol set routes → investigate sounds (including Spore-Talk) → chase on sight → return to patrol after losing target for 15 seconds
- AI: pathfinding with sound-based alert system (player actions generate "noise" values)
- Purpose: create stealth/horror gameplay shift in Necroveil
- Special: at Infection 90+, players can "attune" at a Control Node to command the nearest Hunter for 60 seconds (it attacks enemies instead of players)

## Ecosystem Simulation (Rain World Inspired)

Enemies interact with each other independently of the player:

### Food Chain Rules
1. Tier 1 creatures wander, feed on environmental resources (fungal nodes)
2. Tier 2 creatures hunt Tier 1. If a room has been unvisited for 10+ minutes, Tier 2 may have consumed all Tier 1 (safer room, less loot/Silk)
3. Tier 3 creatures hunt Tier 2. A Tier 3 spawn makes a room dangerous but clears Tier 2
4. Simulation ticks forward when player enters a room (retroactive based on time since last visit)
5. Dead creatures respawn on room re-entry at 50% of original count

### Noise System
Every player action generates a noise value (integer 0-100):

| Action | Noise Value |
|---|---|
| Walking | 5 |
| Running | 15 |
| Jumping | 10 |
| Dash | 20 |
| Light attack | 25 |
| Heavy attack | 40 |
| Ability activation | 30-60 (varies) |
| Spore-Talk (voice active) | 50 |
| Spore Plan execution | 80 |
| Death/Overload | 100 |

Noise decays at -10/second. Tier 2+ enemies investigate noise sources above 30. Mycelium Hunters investigate above 20.

## Boss Design Framework

Every boss follows a structured design template:

### Template

| Phase | HP Threshold | New Mechanics | Arena Change |
|---|---|---|---|
| Phase 1 | 100%-66% | Core attack pattern (3-4 attacks). Teaches player the rhythm. | Standard arena |
| Phase 2 | 66%-33% | New attacks added. One core attack becomes harder (faster/wider). | Arena hazard activates |
| Phase 3 | 33%-0% | Desperation mode. All attacks faster. One unique "finale" attack. | Arena fully active |

### Zone Boss Roster

| Zone | Boss | Phase 1 | Phase 2 | Phase 3 |
|---|---|---|---|---|
| 1: Sporehaven | Spore Mother | Spawn Tier 1 adds, ground slam | Adds are Tier 2, slam creates spore clouds | Continuous add spawns, room fills with clouds |
| 2: Cortex | The Architect | Teleport + projectile volleys | Input-reversal AoE zones appear | Multiple simultaneous clone attacks |
| 3: Ferment Pits | The Distiller | Acid spray cone + charge | Arena floor starts flooding with acid | Fully submerged except 3 platforms, must jump between |
| 4: Chitinhall | Hive Queen | Egg sac spawns + melee combos | Swarm event every 30s during fight | Queen becomes mobile (was stationary), faster eggs |
| 5: Rot Garden | The Gardener | Vine whip + petal scatter | Garden sections bloom/decay (dynamic cover) | Weeping AoE (tears are projectiles), tragic |
| 6: Myco-Forge | The Smelter | Hammer slams + lava geysers | Conveyor belts activate, arena moves | Molten form (faster, trail of lava, shorter reach) |
| 7: Necroveil | Veil Warden | Reality clone (2 versions, one real, one shadow) | Arena warps (rooms within rooms) | 4 simultaneous clones, all can damage you |
| 8: Necrospore Core | Mycelium Heart | Tentacle sweeps + spore barrage | Gravity shifts (ceiling becomes floor) | Mycelium speaks directly to each player, unique attacks per class |

### AI Director (Zone 8 Only)

The Necrospore Core uses a real-time AI Director that adjusts difficulty:

**Inputs:**
- Party average infection level
- Party boon loadout (identifies strongest status effects)
- Recent death rate (rolling 5-minute window)
- Time spent in current room
- Heat level (from Pact)

**Outputs:**
- Enemy composition selection (counters party's strongest boons)
- Hazard intensity (scales with low death rate — pushes complacent parties)
- Boon offering quality (increases after streaks of hard rooms — rubber-banding)
- Boss phase transition speed (faster for high-skill parties, slower for struggling ones)

The Director never makes the game impossible. It targets a 30-40% room failure rate for the session.
