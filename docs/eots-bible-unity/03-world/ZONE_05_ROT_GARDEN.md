# Zone 05 -- The Rot Garden

## Overview

| Property | Value |
|---|---|
| Biome | Decaying Paradise |
| Lock Ability | Decompose (from Chitinhall path) |
| Color Temperature | Faded rose (#C48B9F) + necrotic green (#556B2F) |
| Music Key | Ab major (bittersweet), 65 BPM, solo cello + decaying reverb |
| Room Count Range | 20-35 |
| Atmosphere | Melancholy, faded grandeur, beauty in decay |
| Estimated Clear Time | 12-20 min |
| Fossil Mural Count | 5 (Glacine civilization -- most murals, most tragic story) |

## Narrative Role

The Rot Garden is the Mycelium's memory of paradise. Once the most beautiful zone in the network, it is now in terminal decline -- flowers that bloomed for millennia are wilting, pathways crumble underfoot, and the air carries the scent of endings. The Glacine civilization lived here longest of all the lost peoples, and their story is the bible's emotional core: they saw the decay coming, chose to stay, and composed their final songs as the garden consumed them. The Gardener boss is the last Glacine, still tending a garden that no longer grows.

## Decay Timer Mechanic

The Rot Garden's signature system. Every room has a Decay Timer that begins counting down when the player enters.

### Decay Timer Stages

| Stage | Time Elapsed | Effect | Visual Indicator |
|---|---|---|---|
| Fresh | 0-30s | No penalty. Room at full beauty. | Full color saturation, flowers bloom |
| Wilting | 30-60s | Platforms begin crumbling (edges lose 1 tile/10s) | Color desaturation 20%, petals drift |
| Decaying | 60-90s | Entropy Fields expand (cover 40% of floor). 1% stat drain/s in fields. Enemy HP regenerates 2%/s. | Desaturation 50%, cracks in geometry |
| Rotting | 90-120s | All platforms reduced to minimum size. Entropy Fields cover 70%. Enemy damage +30%. | Desaturation 80%, heavy particle decay |
| Collapsed | 120s+ | Room becomes a DPS check: 3% HP drain/s everywhere. No safe ground. | Monochrome, structural collapse animation |

### Decay Timer Rules

- Timer is per-room, not global. Entering a new room resets to Fresh.
- **Revisiting:** Returning to a previously visited room starts at the stage it was left in, plus one stage advancement. A room left at Wilting returns at Decaying.
- **Pause Mechanic:** Interacting with a Glacine Memory Bloom (2 per room, destructible) pauses the timer for 15s. Each bloom can only be used once.
- **Boss Exception:** The Gardener arena has a fixed 180s total timer across all phases. No pausing.
- **Co-op Scaling:** Timer duration extends by 15s per additional player (solo: 120s collapse, 4-player: 165s collapse).

### Entropy Field Specification

| Property | Value |
|---|---|
| Visual | Shimmering necrotic green ground fog with floating spore particles |
| Stat Drain | 1% of current HP, Stamina, and Ability Charge per second |
| Growth Rate | Expands from room edges inward, 1 tile/10s during Decaying stage |
| Counterplay | Decompose ability clears a 3-tile radius for 8s. Certain boons grant entropy immunity. |

## Room Type Distribution

| Room Type | Min | Max | Notes |
|---|---|---|---|
| Combat | 7 | 13 | No Tier 1 enemies -- they have all decayed. Tier 2-3 only. |
| Puzzle | 3 | 5 | Restoration puzzles: reassemble decayed pathways before timer expires |
| Reward | 3 | 5 | Boon offerings include entropy-resist and time-extension options |
| Rest | 2 | 4 | Memory Gardens: timer paused, Glacine lore plays as ambient audio |
| Boss | 1 | 1 | The Gardener's conservatory |
| Transition | 3 | 5 | Crumbling bridges and vine-covered archways |
| Secret | 1 | 3 | Higher secret density; Glacine left many hidden memories. 50% spawn chance each. |

## Enemy Roster

| Enemy | Tier | HP | Damage | Behavior |
|---|---|---|---|---|
| Wilt Walker | 2 | 90 | 8 (vine lash) | Shambling plant-creature. Regenerates 3 HP/s in Entropy Fields. Immune to stat drain. |
| Petal Wraith | 2 | 70 | 6 (petal storm AoE, 3-tile radius) | Floating. Teleports to Entropy Field edges. Petal storm applies 2s slow. |
| Rot Hulk | 3 | 180 | 14 (slam), 5 (decay aura 2-tile radius) | Slow melee brute. Decay aura accelerates room timer by 2x while alive. Kill priority. |
| Memory Shade | 2 | 60 | 0 (debuff) | Glacine ghost. Does not attack. Touching one triggers a 3s vision of the past (player is immobile but invulnerable). Grants 10 Echoes. |
| Bloom Parasite | 2 | 85 | 10 (vine burst) | Disguised as a Memory Bloom. Attacking a Bloom has a 20% chance of revealing a Parasite instead. |

### Mini-Boss: The Arborist

| Property | Value |
|---|---|
| Tier | 3 |
| HP | 450 |
| Damage | 16 (root eruption), 10 (seed volley) |
| Behavior | Roots itself in center. Spawns Wilt Walkers from root nodes. Root eruptions track player position with 1s delay. Uproots and relocates at 50% HP. |
| Spawn Chance | 40% per run |

## Boss: The Gardener

| Phase | HP Pool | Mechanics |
|---|---|---|
| Phase 1 (100-65%) | 650 | The Gardener tends flower beds, ignoring the player for 5s. Then attacks with sorrowful vine sweeps (10 damage, wide arc). Periodically plants Memory Blooms that heal the Gardener 20 HP if not destroyed. |
| Phase 2 (65-30%) | 455 | Gardener weeps openly (audio: sobbing layered with cello). Arena splits into quadrants -- 2 are Entropy Fields. Vine attacks become faster. Gardener plants Bloom Parasites alongside real Blooms. |
| Phase 3 (30-0%) | 200 | Gardener stops attacking. Kneels in the center, channeling a garden restoration. If the restoration completes (30s channel), the garden heals and the fight resets to Phase 1. Players must interrupt by dealing damage, but each hit triggers a guilt-inducing voice line. |

**Design Intent:** The most emotionally complex boss in the game. Phase 3 forces players to actively destroy something beautiful. The Gardener never fights in Phase 3 -- the player becomes the aggressor. This fight should make players feel something.

## Hazard List

| Hazard | Effect | Duration | Telegraph |
|---|---|---|---|
| Entropy Field | 1% all-stat drain per second | Persistent, expanding | Necrotic green fog, visible boundary |
| Crumbling Platform | Platform loses 1 tile from edges every 10s | Permanent | Cracks appear, dust particles fall |
| Petal Storm | 4 damage/s + 30% slow in AoE | 5s | Petals begin swirling 2s before full damage |
| Root Snare | Immobilizes player for 2s | 2s | Roots visible beneath semi-transparent floor, 1s warning |

## Art Direction

**Palette:** Faded rose (#C48B9F) for organic elements -- flowers, vines, canopy light. Necrotic green (#556B2F) for decay zones and entropy effects. Gold (#C9A84C) for Glacine artifacts and murals. Color saturation is tied directly to the Decay Timer.

**Architecture:** A once-magnificent botanical garden in terminal decline. Marble columns overgrown with dying vines. Fountains that trickle brown water. Grand arches with crumbling keystones. The beauty is still visible beneath the rot -- this is not ugliness but tragedy.

**Environmental Storytelling:** Glacine possessions scattered throughout -- musical instruments, gardening tools, love letters carved into stone. None are interactable; they exist to build atmosphere.

**Audio:** Solo cello carries the melodic theme (Ab major, bittersweet). As decay timer advances, the reverb tail extends and distorts. Phase 3 of the Gardener fight uses the Glacine's farewell song -- a melody introduced in the zone's rest rooms, now heard in full.

## Level Design Guidelines

1. **Beauty First:** Every room must be visually beautiful at the Fresh stage. The decay's emotional impact depends on players seeing what is being lost.
2. **Timer Pressure:** The decay timer should create urgency without panic. Average room clear time should be 45-60s for skilled players, leaving a comfortable margin before Decaying stage.
3. **Memory Shade Placement:** At least 2 Memory Shades per run. Their visions are non-interactive cutscenes showing Glacine daily life. They are lore delivery, not gameplay obstacles.
4. **Secret Density:** The Rot Garden has the highest secret room density. The Glacine hid their most precious memories in sealed chambers. Secrets reward lore and high-tier Echoes.
5. **No Cheap Deaths:** The Collapsed stage (120s+) should be reachable but rare. It exists as a fail state for players who are lost, not as a routine challenge.
6. **Emotional Pacing:** Place a rest room (Memory Garden) immediately before the boss corridor. Players need a quiet moment before the Gardener fight.
7. **Mural Sequence:** The 5 Glacine murals must be encountered in narrative order across the zone. They tell a single story: arrival, flourishing, first signs of decay, acceptance, and farewell.
