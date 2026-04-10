# Zone 07 -- The Necroveil

## Overview

| Property | Value |
|---|---|
| Biome | Death Membrane Interface |
| Lock Ability | Echo Sight (requires Spore Weld + Decompose, i.e., Zones 5 and 6 cleared) |
| Color Temperature | Cold void purple (#2C003E) + hot arterial red (#8B0000) |
| Music Key | Atonal, 50 BPM, sub-bass drones + whispered vocals |
| Room Count Range | 25-40 |
| Atmosphere | Existential dread, liminality, the space between alive and dead |
| Estimated Clear Time | 20-35 min |
| Fossil Mural Count | 4 (Umbral civilization -- partially corrupted, incomplete) |

## Narrative Role

The Necroveil is the membrane between the living Mycelium and the dead Necrospore network. It is the threshold players must cross to reach the endgame. The Umbral civilization existed in both states simultaneously -- alive and dead, present and absent. Their corrupted murals are incomplete on purpose: the Umbral understood that some truths cannot be fully observed, only approached. The Veil Warden is a guardian that exists across multiple timelines, fighting the player from realities that have already ended.

## Mycelium Hunter Specification

The Necroveil's defining threat. Mycelium Hunters are near-invincible pursuers that patrol the zone. Stealth is the primary survival verb.

### Hunter Stats

| Property | Value |
|---|---|
| HP | 5000 (effectively unkillable by normal means) |
| Damage | 40 (grab) -- instant down state at most player HP levels |
| Speed | 120% of player sprint speed |
| Detection Range | 12 tiles (visual), 8 tiles (audio) |
| Patrol Count | 1 per 8 rooms (3-5 active per run) |

### Hunter Patrol Patterns

| Pattern | Description | Rooms Used |
|---|---|---|
| Linear Sweep | Walks a fixed path through 3-4 rooms in sequence, then reverses. Predictable. | Transition corridors |
| Sentinel | Stands at a fixed point, rotates 360 degrees over 10s. Blocks chokepoints. | Room entrances, puzzle gates |
| Roamer | Random walk within a 5-room radius. Changes direction every 15-20s. Unpredictable. | Combat and reward rooms |
| Stalker | Follows the player's last known position. Searches for 30s, then returns to nearest patrol route. | Triggered by detection |
| Pack (Alarm Level 3 only) | Two Hunters coordinate to sweep from opposite ends of a corridor. | Late-zone only, max 1 pack per run |

### Detection and Noise Thresholds

| Noise Source | Noise Level | Detection Radius |
|---|---|---|
| Crouching/still | 0 | Undetectable (unless in line of sight within 4 tiles) |
| Walking | 1 | 3 tiles |
| Running | 3 | 6 tiles |
| Light attack | 4 | 7 tiles |
| Heavy attack / ability | 6 | 10 tiles |
| Spore-Talk (voice chat) | 5 | 8 tiles |
| Enemy death cry | 4 | 7 tiles |
| Combat with non-Hunter enemies | 5 | 8 tiles (sustained; attracts Stalker pattern) |

### Hunter State Machine

| State | Behavior | Transition Trigger |
|---|---|---|
| Patrol | Follows assigned pattern. Ignores players outside detection range. | Default state |
| Alert | Stops. Head turns toward noise source. Listens for 3s. | Noise Level 2+ within detection radius |
| Investigate | Moves to noise source at 80% speed. Searches 5-tile radius for 15s. | Second noise event during Alert, or Noise Level 4+ |
| Chase | Full speed pursuit. Will follow through room transitions. Attacks on contact. | Visual confirmation of player |
| Search | Returns to last-seen position. Searches 8-tile radius for 30s. | Loses visual on player for 5s during Chase |
| Return | Walks back to nearest patrol route point. | Search timer expires with no contact |

### Hunter Counterplay

| Method | Effect | Limitation |
|---|---|---|
| Stealth (crouching, no actions) | Complete invisibility if out of sight line | Cannot progress while hiding |
| Distraction (throw object) | Draws Hunter to impact point for 10s | 1 distraction item per 3 rooms |
| Echo Sight ability | Reveals Hunter positions through walls for 8s | 30s cooldown |
| Spore Cloud grenade | Blinds Hunter for 5s, resets to Patrol state | Rare consumable, 2-3 per run |
| Infection 90+ | Hunters become allied, ignore player entirely | Extreme infection cost; endgame-only |

## Room Type Distribution

| Room Type | Min | Max | Notes |
|---|---|---|---|
| Combat | 8 | 14 | Tier 3 dominant. Combat noise attracts Hunters -- fight fast or pay the price. |
| Puzzle | 3 | 5 | Reality-shift puzzles: interact with objects in two overlapping realities |
| Reward | 3 | 5 | Stealth boons and distraction items weighted heavily |
| Rest | 3 | 5 | Hunter-proof sealed rooms. Higher count than other zones -- players need breathing room. |
| Boss | 1 | 1 | Veil Warden arena (Hunter-free) |
| Transition | 5 | 8 | Long corridors with Hunter patrol routes -- the zone's tension peaks |
| Secret | 0 | 2 | Phase through corrupted walls using Echo Sight; Umbral fragments |

## Enemy Roster

| Enemy | Tier | HP | Damage | Behavior |
|---|---|---|---|---|
| Veil Shade | 2 | 75 | 8 (phase strike) | Phases in/out of visibility on 3s cycle. Can only be damaged while visible. Death cry is silent (does not attract Hunters). |
| Membrane Crawler | 2 | 95 | 10 (lunge) | Moves on walls and ceilings. Lunges from above with 0.8s telegraph. Loud death cry (attracts Hunters). |
| Reality Fracture | 3 | 0 (environmental) | 0 (teleport) | Not an enemy -- a hazard entity. Contact teleports player to a random room. Cannot be destroyed, only avoided. |
| Echo Remnant | 3 | 150 | 12 (mimic attack) | Copies the player's last 3 attack patterns and uses them against the party. Immune to the attack type it is mimicking. |
| Void Blossom | 2 | 60 | 5 (AoE drain, 3-tile) | Stationary. Drains HP/Echoes in radius. Silent -- no noise on death. Rewards 15 Echoes. |

### Mini-Boss: Phase Stalker

| Property | Value |
|---|---|
| Tier | 3 |
| HP | 600 |
| Damage | 18 (phase claw), 25 (reality tear -- line AoE) |
| Behavior | Exists in two realities simultaneously. Visible in one, invisible in the other. Players must use Echo Sight to track both versions. Only damageable when both versions overlap (every 12s for 4s). |
| Spawn Chance | 50% per run |

## Boss: The Veil Warden

| Phase | HP Pool | Mechanics |
|---|---|---|
| Phase 1 (100-65%) | 900 | Arena is a stable platform over void. Warden exists as 3 simultaneous versions (past, present, future). Only the "present" version takes damage. All 3 attack independently. Past-Warden uses slow heavy attacks. Future-Warden uses fast projectiles. Present-Warden uses mid-range combos. |
| Phase 2 (65-30%) | 630 | Arena splits into 3 floating platforms (one per timeline). Players are randomly assigned to platforms. Each platform has one Warden version. Platforms reconnect every 20s for 5s. Must coordinate damage during reconnection windows. |
| Phase 3 (30-0%) | 280 | All three versions merge. Single Warden with combined moveset. Arena warps -- gravity shifts 90 degrees every 15s (walls become floors). Final 10% HP: Warden stops, delivers a lore monologue about what lies beyond the veil, then resumes with a desperation attack pattern. |

**Design Intent:** The most mechanically complex boss. Tests spatial awareness, coordination (in co-op), and adaptability. The timeline mechanic rewards players who observe attack patterns rather than reacting purely on reflex.

## Hazard List

| Hazard | Effect | Duration | Telegraph |
|---|---|---|---|
| Reality Fracture | Teleports to random room | Instant | Swirling void portal, visible at all times |
| Void Tendril | 12 damage + pull toward nearest fracture | Instant | Dark tendrils reach from fracture, 1s warning |
| Phase Flicker | Player becomes invisible to allies for 4s (co-op only) | 4s | Screen static, audio cut |
| Gravity Inversion | Reverses gravity for 3s in a 4x4 tile area | 3s | Floor tiles glow purple, 2s warning |

## Art Direction

**Palette:** Cold void purple (#2C003E) is the dominant background -- deep, near-black, infinite. Hot arterial red (#8B0000) marks living elements: blood vessels in walls, enemy cores, the Warden's heart. White (#F0F0F0) is used sparingly for reality fracture edges and Echo Sight overlays.

**Architecture:** The membrane between life and death rendered as architecture. Walls are translucent tissue showing void beyond. Floor surfaces pulse with vein-like channels. Rooms appear to float in infinite darkness. Doorways are sphincter-like openings that iris open.

**Lighting:** Minimal. Primary light comes from the player's own bioluminescence and enemy cores. Hunters emit a cold blue glow visible through walls (intentional -- players should see them coming). The Warden arena uses dramatic spotlighting that shifts with phase transitions.

**Audio:** Sub-bass drone at the edge of hearing (25 Hz). Whispered vocals use processed recordings of real speech played in reverse. Hunter footsteps are a distinct rhythmic thud audible from 10 tiles away (audio is the primary detection tool). Combat music is absent -- fights occur over the ambient drone, heightening tension.

## Level Design Guidelines

1. **Hunter Density:** Never more than 1 Hunter per 2 connected rooms. Players must have escape routes. A room with a Hunter should always have at least 2 exits.
2. **Stealth Infrastructure:** Every room must include at least 2 hiding spots (alcoves, overhangs, or phase-through objects). Hiding must be a viable verb at all times.
3. **Rest Room Frequency:** Higher than any other zone (1 per 5-7 rooms). The sustained tension requires regular relief.
4. **Sound Design Primacy:** The zone is designed to be played with audio. Visual cues exist for accessibility, but the intended experience is hearing Hunters before seeing them.
5. **Umbral Murals:** Placed in secret rooms only. They are fragmented and partially corrupted -- the player must piece together the narrative from incomplete information. This mirrors the Umbral's philosophy.
6. **Infection Gate:** At Infection 90+, the entire zone transforms. Hunters become passive, enemies ignore the player, and the Warden fight changes dialogue. This is the "dark path" experience -- powerful but deeply compromised.
7. **No Dead Ends:** Every corridor must loop or branch. Being cornered by a Hunter with no exit is unfair, not tense.
