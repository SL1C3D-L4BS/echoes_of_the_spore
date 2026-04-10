# 03 — WORLD ARCHITECTURE & BIOME SYSTEM

## Three-Layer Hierarchy

### Layer 1: The Canopy (Persistent Hub)

**Function:** Social space, loadout preparation, quest hub, Community Tree visualization, lore archive, matchmaking, purification, crafting (future).

**Technical Implementation:**
- Single persistent map per server, loaded when player is in hub
- State stored in Supabase: structure visibility flags, NPC states, mural unlock states
- Synced to all connected clients via Supabase Realtime subscriptions
- Player positions in hub synced via Redis at 2 Hz (non-gameplay, cosmetic only)
- Maximum 200 visible players in hub at once (beyond: instanced overflow hubs)

**Evolution Stages:**

| Stage | Community Tree Depth | Visual State | Structures Available |
|---|---|---|---|
| Seed | 0 | Bioluminescent clearing, single bench | Bench, Zone 1 gate |
| Sprout | 1-5 | Small fungal shelters grow | Basic merchant, Wish Board |
| Growth | 6-10 | Canopy roof begins forming | Blacksmith, Lore Pedestal, Zone 2-4 gates |
| Bloom | 11-15 | Full canopy overhead, paths branch | Fast-travel nodes, Purification Pool, Housing |
| Apex | 16-19 | Metropolis-scale fungal city | All services, Rite Arena, Cross-server portal |
| Ascended / Withered | 20 (post-vote) | Flourishing growth OR beautiful decay | Final state, permanent |

### Layer 2: The Zones (Fixed Macro-Structure)

Eight zones arranged in a directed acyclic graph (DAG). Zone order is fixed; room content is procedural.

**Zone Connectivity Graph:**

```
Sporehaven (start)
├── The Cortex (requires: —)
│   ├── Fermentation Pits (requires: Synapse Link)
│   │   └── Myco-Forge (requires: Acid Membrane + Synapse Link)
│   └── Chitinhall (requires: Synapse Link)
│       └── The Rot Garden (requires: Carapace Shift)
└── (shortcut from Sporehaven to Rot Garden after Community Tree Expansion Depth 8)

Myco-Forge + Rot Garden → The Necroveil (requires: Spore Weld + Decompose)
The Necroveil → Necrospore Core (requires: Echo Sight + all prior abilities)
```

**Zone Detail Specifications:**

#### Zone 1: Sporehaven

| Property | Value |
|---|---|
| Biome | Bioluminescent Marsh |
| Lock Ability | None (starting zone) |
| Hazard | Spore Clouds: reduce vision radius by 40% for 5 seconds |
| Atmosphere | Wonder, safety, introduction |
| Color Temperature | Warm green (#2ECC71) + cool cyan (#00FFD1) |
| Music Key | D minor, 90 BPM, ambient pads + plucked strings |
| Room Count Range | 15-25 |
| Enemy Tier Range | Tier 1 only, Tier 2 mini-boss |
| Fossil Mural Count | 3 (Progenitor civilization) |
| Unique Mechanic | Tutorial integration — first Fusion Node, first Boon offering |
| Boss | The Spore Mother (stationary, spawns adds, teaches pattern recognition) |
| Estimated Clear Time | 8-12 minutes (first run), 4-6 minutes (experienced) |

#### Zone 2: The Cortex

| Property | Value |
|---|---|
| Biome | Neural Fungal Network |
| Lock Ability | Synapse Link (granted by Spore Mother clear) |
| Hazard | Thought Parasites: reverse left/right input for 3 seconds on contact |
| Atmosphere | Disorientation, alien beauty |
| Color Temperature | Deep purple (#6C3483) + gold (#C9A84C) |
| Music Key | F# diminished, 75 BPM, pulsing synths + dissonant choir |
| Room Count Range | 20-30 |
| Enemy Tier Range | Tier 1-2, Tier 2 mini-boss |
| Fossil Mural Count | 3 (Luminari civilization) |
| Unique Mechanic | Synapse Puzzles — connect neural pathways by activating nodes in sequence |
| Boss | The Architect (teleporting caster, spawns Thought Parasite adds) |
| Estimated Clear Time | 10-15 minutes |

#### Zone 3: Fermentation Pits

| Property | Value |
|---|---|
| Biome | Acidic Brewery Caves |
| Lock Ability | Acid Membrane (granted by Architect clear OR Community Tree Evolution Depth 3) |
| Hazard | pH Storms: zones of acid rain dealing 2 DPS for 8 seconds, telegraphed 2 seconds before |
| Atmosphere | Disgust, industrial grime, dark humor |
| Color Temperature | Sickly yellow-green (#A3B818) + corrosion orange (#D4731A) |
| Music Key | Bb minor, 110 BPM, industrial percussion + distorted bass |
| Room Count Range | 20-35 |
| Enemy Tier Range | Tier 1-2, Tier 3 rare spawn |
| Fossil Mural Count | 4 (Ferrox civilization) |
| Unique Mechanic | pH Management — certain abilities change the room's acidity, affecting enemy behavior |
| Boss | The Distiller (phase-based, arena floods with acid between phases) |
| Estimated Clear Time | 12-18 minutes |

#### Zone 4: Chitinhall

| Property | Value |
|---|---|
| Biome | Insectoid Hive Architecture |
| Lock Ability | Carapace Shift (alternate path from Cortex) |
| Hazard | Swarm Events: every 90 seconds, a wave of 20-30 Tier 1 swarmer enemies floods the room for 15 seconds |
| Atmosphere | Claustrophobia, buzzing tension, organized menace |
| Color Temperature | Warm amber (#DAA520) + cold grey (#708090) |
| Music Key | E minor, 130 BPM, staccato strings + rhythmic clicking |
| Room Count Range | 25-40 |
| Enemy Tier Range | Tier 1 (swarms), Tier 2 (elites), Tier 3 (rare) |
| Fossil Mural Count | 3 (Verdant civilization) |
| Unique Mechanic | Hive Alarm — loud actions (abilities, Spore-Talk) attract additional swarmers |
| Boss | The Hive Queen (summons waves, must destroy egg sacs between phases) |
| Estimated Clear Time | 15-22 minutes |

#### Zone 5: The Rot Garden

| Property | Value |
|---|---|
| Biome | Decaying Paradise |
| Lock Ability | Decompose (from Chitinhall path) |
| Hazard | Entropy Fields: standing in marked zones drains 1% of all stats per second |
| Atmosphere | Melancholy, faded grandeur, beauty in decay |
| Color Temperature | Faded rose (#C48B9F) + necrotic green (#556B2F) |
| Music Key | Ab major (bittersweet), 65 BPM, solo cello + decaying reverb |
| Room Count Range | 20-35 |
| Enemy Tier Range | Tier 2-3, no Tier 1 (they've all been consumed) |
| Fossil Mural Count | 5 (Glacine civilization — most murals, most tragic story) |
| Unique Mechanic | Decay Timer — rooms slowly deteriorate, platforms crumble, paths close. Revisiting costs more time. |
| Boss | The Gardener (tragic fight, weeps while attacking, phases involve restoring/destroying garden sections) |
| Estimated Clear Time | 12-20 minutes |

#### Zone 6: Myco-Forge

| Property | Value |
|---|---|
| Biome | Biomechanical Foundry |
| Lock Ability | Spore Weld (from Fermentation Pits path) |
| Hazard | Pressure Locks: timed platforming sequences where doors close on a timer |
| Atmosphere | Industry, purpose, heat, the Mycelium's workshop |
| Color Temperature | Hot orange (#FF6B35) + deep charcoal (#1A1A2E) |
| Music Key | C minor, 140 BPM, hammering percussion + brass stabs |
| Room Count Range | 20-35 |
| Enemy Tier Range | Tier 2-3, unique Construct enemies |
| Fossil Mural Count | 3 (Pyreth civilization) |
| Unique Mechanic | Forging — spend Echoes at forge stations to temporarily upgrade weapons mid-run (lost on death/exit) |
| Boss | The Smelter (molten arena, environmental puzzle + combat hybrid) |
| Estimated Clear Time | 15-25 minutes |

#### Zone 7: The Necroveil

| Property | Value |
|---|---|
| Biome | Death Membrane Interface |
| Lock Ability | Echo Sight (requires both Spore Weld + Decompose, i.e., both Zone 5 and 6 cleared) |
| Hazard | Reality Fractures: portals that teleport players to random rooms within the zone |
| Atmosphere | Existential dread, liminality, the space between alive and dead |
| Color Temperature | Cold void purple (#2C003E) + hot arterial red (#8B0000) |
| Music Key | Atonal, 50 BPM, sub-bass drones + whispered vocals |
| Room Count Range | 25-40 |
| Enemy Tier Range | Tier 3 dominant, Mycelium Hunters patrol |
| Fossil Mural Count | 4 (Umbral civilization — murals are partially corrupted, incomplete) |
| Unique Mechanic | Mycelium Hunters: near-invincible pursuers (see Section 08). Stealth becomes primary verb. At Infection 90+: command Hunters as allies. |
| Boss | The Veil Warden (reality-warping fight, arena shifts mid-combat, multiple simultaneous versions of boss from parallel timelines) |
| Estimated Clear Time | 20-35 minutes |

#### Zone 8: Necrospore Core

| Property | Value |
|---|---|
| Biome | Heart of the Dead Network |
| Lock Ability | ALL prior abilities required |
| Hazard | All hazards from all zones, randomized per room. AI Director selects based on party composition. |
| Atmosphere | Transcendence, cosmic horror, finality |
| Color Temperature | Necrospore Gold (#C9A84C) + void black (#0A0A0F) |
| Music Key | Polymodal, 160 BPM building to silence, full orchestra + electronic + choir |
| Room Count Range | 30-40 |
| Enemy Tier Range | Tier 3 standard, unique Core enemies |
| Fossil Mural Count | 3 (Progenitor endgame — the truth about the Mycelium's creation) |
| Unique Mechanic | AI Director: dynamically adjusts difficulty, hazard selection, and enemy composition based on party's infection level, boon loadout, and recent performance. |
| Boss | The Mycelium Heart (multi-phase, requires all 4 players to survive specific mechanics, the Mycelium talks to you directly during the fight) |
| Estimated Clear Time | 25-45 minutes |

### Layer 3: Rooms (Procedural Micro-Structure)

Rooms are generated per-delve from the Rust proc-gen engine. Each room is defined by:

| Property | Type | Range |
|---|---|---|
| Room Type | Enum | Combat, Puzzle, Reward, Rest, Boss, Transition, Secret |
| Grid Size | int × int | 40×24 to 80×40 tiles |
| Tile Size | int | 16×16 px base |
| Enemy Count | int | 0 (Rest) to 20 (Swarm) |
| Hazard Count | int | 0 to 5 |
| Micro-Gate Count | int | 0 to 3 |
| Entry Points | array | 2-4 cardinal connections |
| Loot Tier | Enum | Common, Uncommon, Rare, Legendary |

Room generation algorithm is detailed in Section 15 (Procedural Generation).
