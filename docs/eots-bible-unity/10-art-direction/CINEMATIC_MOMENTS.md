# Cinematic Moments

## Overview

Echoes of the Spore has no pre-rendered cutscenes. All cinematic moments are real-time, in-engine sequences using controlled camera movement, timed VFX, and audio cues. Player input is restricted but the game never fully takes control away from the player for more than 10 seconds (except the True Ending Revelation).

## Cinematic Inventory

| Category | Count | Avg Duration | Player Control |
|----------|-------|-------------|---------------|
| Boss Intros | 8 | 5s | None (camera pans) |
| Boss Deaths | 8 | 4s | Restricted (can move, no attack) |
| Zone Transitions | 7 | 3s | Walking only |
| True Ending Vote | 1 | 120s (longest) | Walking + vote interaction |
| Community Tree Milestones | 10+ | 3s | Full (HUD overlay only) |

## Boss Intros

### Camera Choreography

All boss intros follow a three-beat structure:

| Beat | Duration | Camera | Player | Audio |
|------|----------|--------|--------|-------|
| 1: Approach | 1.5s | Slow zoom out from player to reveal arena | Walking into arena, passes fog gate | Music fades to silence |
| 2: Reveal | 2.0s | Pan to boss, slight dolly toward boss face/core | Frozen in place | Boss leitmotif stinger (2s) |
| 3: Title | 1.5s | Snap back to gameplay framing, slight shake | Control returns | Battle music begins |

Title card appears during Beat 3 (see DIALOGUE_SYSTEM.md for card format).

### Per-Boss Reveal Details

| Boss | Reveal Camera Motion | Unique Element |
|------|---------------------|----------------|
| Spore Mother | Tilt up from roots to canopy crown | Spores cascade down during reveal |
| The Architect | Spiral pan following neural pathways to center | Synaptic arcs flash in sequence |
| The Distiller | Slow descent revealing vat-filled arena | Acid bubbles breach surface |
| Hive Queen | Pan across collapsed honeycomb to throne | Chitin plates reassemble around her |
| The Gardener | Gentle pan across dying garden to kneeling figure | A single flower blooms as camera passes |
| The Smelter | Fast track along conveyor to forge core | Sparks erupt on beat |
| Veil Warden | Camera stutters/glitches, reality fragments | Screen tears reveal the Warden between fragments |
| Mycelium Heart | 360-degree slow rotation revealing the chamber | Every surface pulses in unison |

### Boss Intro Timing

| Parameter | Value |
|-----------|-------|
| Total duration | 5.0s (fixed, all bosses) |
| Skip option | Hold interaction button for 1s (solo only). Multiplayer: majority vote skip. |
| Mycelium Voice | Boss Intro line plays at Beat 2 start (Priority 1, ignores cooldown) |
| First encounter only | Full intro plays once per character per boss. Subsequent fights: 2s shortened version (Beat 2 + 3 only). |
| Death replay | Shortened version always on retry |

## Zone Transitions

### Sequence

| Step | Duration | Visual | Audio |
|------|----------|--------|-------|
| 1: Exit current zone | 1.0s | Player walks through transition door | Current zone music fades (1s) |
| 2: Transition corridor | 1.5s | Narrow corridor, tiles blend between zones | Silence + ambient reverb shift |
| 3: Enter new zone | 0.5s | Camera widens to reveal new zone vista | New zone music fades in (1s) |

### Transition Corridor Rules

| Rule | Detail |
|------|--------|
| Length | 8-12 tiles horizontally |
| Tileset | Left half uses source zone tiles, right half uses destination tiles |
| Lighting | Crossfade from source zone ambient to destination zone ambient |
| Parallax | Background layers crossfade during walk |
| Enemies | None in transition corridors |
| Player speed | Normal movement, no restrictions |

### Zone Name Reveal

On entering a new zone for the first time per delve:

| Element | Specification |
|---------|--------------|
| Zone number | Small text, Spore White, Space Grotesk Regular 18px |
| Zone name | Large text, Necrospore Gold, Space Grotesk Bold 36px |
| Position | Center screen, 30% from top |
| Animation | Fade in 0.5s, hold 2.0s, fade out 0.5s |
| Audio | Zone-specific ambient stinger (0.5s) |

## True Ending Vote Sequence

### The Revelation (120 seconds total)

| Phase | Time | Camera | Content |
|-------|------|--------|---------|
| 1: Silence | 0-10s | Static on defeated Mycelium Heart | No audio. No input. Screen breathes. |
| 2: Awakening | 10-20s | Slow zoom into Heart center | Heartbeat audio begins, low |
| 3: Voice begins | 20-110s | Environmental pan across the chamber | Mycelium Voice monologue (90s). Camera shows world reacting. |
| 4: The Question | 110-120s | Pan to reveal FEED and STARVE murals | Voice asks the question. Camera settles between murals. |
| 5: Vote opens | 120s+ | Returns to gameplay camera | Player can move freely, approach portals |

### Player Interaction During Vote

| Action | Allowed |
|--------|---------|
| Movement | Yes |
| Combat | No (no enemies present) |
| Menu access | Yes |
| Return to Canopy | Yes (can vote from hub) |
| Communication | Spore-Talk active |

## Community Tree Milestone Celebrations

### Trigger

When a Community Tree branch reaches a milestone (10%, 25%, 50%, 75%, 100%), all online players see a celebration.

### Sequence

| Step | Duration | Visual | Audio |
|------|----------|--------|-------|
| 1: Alert | 0.5s | Toast notification: "The Canopy grows..." | Chime (celebration tone) |
| 2: Tree pulse | 1.5s | If in hub: tree model emits burst of particles, new branch glows. If in delve: HUD tree icon pulses. | Ambient swell (1.5s) |
| 3: Reward | 1.0s | Reward popup (cosmetic unlock, modifier, etc.) | Reward fanfare (1s) |

### Celebration Does Not Interrupt Gameplay

| Context | Behavior |
|---------|----------|
| In combat | Toast only (top-right). Full celebration on room clear. |
| In boss fight | Deferred until boss defeated or player returns to hub. |
| In menu | Toast appears over menu. Full celebration on menu close. |
| In Canopy hub | Full celebration plays immediately. |

## Camera System

### Gameplay Camera

| Parameter | Value |
|-----------|-------|
| Type | Orthographic |
| Size | 8 units (adjustable in settings: 7-10) |
| Follow | Smooth follow on player, 0.1s lerp |
| Look-ahead | 2 units in movement direction |
| Room bounds | Camera clamped to current room rect |

### Cinematic Camera Overrides

| Override | Trigger | Return |
|---------|---------|--------|
| Boss intro | Arena entry | After title card (5s) |
| Zone transition | Transition corridor | After entering new zone |
| Mural view | Mural interaction | On exit interaction |
| True Ending | Heart defeat | After vote opens |
| Milestone | Community Tree milestone | After celebration (3s) |

## Implementation Notes

- Camera system uses Cinemachine (Unity) with virtual camera priorities for cinematic overrides
- Boss intros are Timeline assets per boss (`Assets/_Project/Cinematics/BossIntros/`)
- Zone transitions driven by `ZoneTransitionController` with crossfade materials on corridor tiles
- True Ending sequence is a single Timeline asset with Mycelium Voice audio track and camera track
- Community Tree celebrations use `MilestoneCelebrationSystem` listening to server events via `EventBus`
- All cinematic cameras use Cinemachine Confiner for room-bounded framing
- Skip input handled by `CinematicSkipController` with multiplayer vote aggregation
