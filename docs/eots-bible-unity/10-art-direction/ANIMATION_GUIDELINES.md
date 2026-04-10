# Animation Guidelines

## Overview

All character and enemy animations follow a hand-drawn 2D sprite pipeline. Animations are authored as keyframe sequences at 12fps and played back at 60fps with interpolation. This document defines standards for frame counts, sprite sheet layout, state machine structure, and blend rules.

## Keyframe Standard

| Parameter | Value |
|-----------|-------|
| Authoring framerate | 12 fps (one drawing per ~83ms) |
| Playback framerate | 60 fps |
| Interpolation | Linear sprite lerp between keyframes (position, scale, rotation on sub-sprites) |
| Pixel art rule | No sub-pixel rendering. All frames snap to integer pixel positions. |
| Onion skinning | Required during authoring (previous + next frame visible) |

## Character Animation Frame Budget

| State | Keyframes | Loop | Priority | Notes |
|-------|-----------|------|----------|-------|
| Idle | 8 | Yes | Low | Subtle breathing, weapon sway |
| Run | 8 | Yes | Medium | Full stride cycle, 2 contact frames |
| Jump_Up | 4 | No | High | Anticipation into launch |
| Jump_Peak | 2 | Yes | Medium | Held at apex, cloak/hair float |
| Fall | 4 | No | Medium | Accelerating descent pose |
| Land | 3 | No | High | Impact squash into recover |
| Dash | 4 | No | High | Stretch into blur into recover |
| Light_1 | 6 | No | High | Swing arc, 2-frame anticipation |
| Light_2 | 6 | No | High | Follow-up, different angle |
| Light_3 | 8 | No | High | Finisher, heavier impact |
| Heavy_Charge | 4 | Yes | High | Held charging pose, particle buildup |
| Heavy_Release | 8 | No | High | Big swing, 3-frame follow-through |
| Air_Light | 6 | No | High | Aerial slash |
| Air_Heavy | 8 | No | High | Downward slam |
| Hit_Receive | 4 | No | Highest | Knockback pose, I-frame flash |
| Death | 12 | No | Highest | Collapse into dissolve (shader takes over) |
| Heal_Bind | 8 | No | High | Kneeling pose, fungal glow wrap |
| Ability_Cast | 8-12 | No | High | Per-ability unique animation |

## Sprite Sheet Layout

### Grid Format

- Grid: 4 columns, N rows (ceil(frames / 4))
- Cell size: 128x128 per frame (character), 64x64 (Tier 1 enemy), 96x96 (Tier 2), 256x256+ (boss)
- Padding: 2px transparent border per cell (prevents bleeding in atlas)
- Naming convention: `{character}_{state}_{direction}.png`

### Atlas Packing

| Entity Type | Sheet Count | Total Frames (est.) | Atlas Size |
|-------------|------------|---------------------|------------|
| Player class (each) | 1 per state group | ~120 frames | 2048x2048 |
| Tier 1 enemy | 1 sheet | ~48 frames | 512x512 |
| Tier 2 enemy | 1-2 sheets | ~80 frames | 1024x1024 |
| Tier 3 enemy | 2 sheets | ~120 frames | 2048x1024 |
| Boss | 3-4 sheets | ~200+ frames | 2048x2048 each |

Packed into Unity Sprite Atlas assets. One atlas per entity type per zone for Addressable loading.

## State Machine Transitions

### Animator Controller Structure

```
Entry -> Idle
  Idle -> Run         [speed > 0.1]
  Idle -> Jump_Up     [jump_pressed]
  Idle -> Dash        [dash_pressed]
  Idle -> Light_1     [attack_pressed]
  Idle -> Heavy_Charge [heavy_held]
  Idle -> Heal_Bind   [bind_pressed AND silk_full]

  Run -> Idle         [speed < 0.1]
  Run -> Jump_Up      [jump_pressed]
  Run -> Light_1      [attack_pressed]

  Jump_Up -> Jump_Peak [velocity.y < 0.5]
  Jump_Peak -> Fall    [velocity.y < -0.5]
  Fall -> Land         [grounded]
  Land -> Idle         [animation_complete]

  Light_1 -> Light_2  [attack_pressed, window 0.3-0.6s]
  Light_2 -> Light_3  [attack_pressed, window 0.3-0.6s]
  Light_3 -> Idle     [animation_complete]

  Any -> Hit_Receive  [damage_received]
  Any -> Death        [hp <= 0]
```

### Transition Timing

| Transition | Blend Duration | Type |
|-----------|---------------|------|
| Idle to Run | 0.1s | Crossfade |
| Ground to Jump | 0.0s (instant) | Cut |
| Fall to Land | 0.0s (instant) | Cut |
| Any to Hit | 0.0s (instant) | Cut, interrupts current |
| Attack chain | 0.05s | Quick crossfade |
| Any to Death | 0.0s (instant) | Cut, no interruption allowed |
| Ability to Idle | 0.15s | Crossfade |

## Blend Rules

| Rule | Detail |
|------|--------|
| No mid-attack blend | Attack animations play to completion or until cancel window |
| Cancel windows | Light attacks: cancel into dash at frame 4+. Heavy: no cancel until release. |
| Hit interrupt | Hit_Receive interrupts any non-Death animation immediately |
| Animation priority | Death > Hit > Ability > Attack > Movement > Idle |
| Queued input | One input can be buffered during current animation (3-frame input buffer) |
| Direction flip | Sprite X-scale flips instantly. No blend for direction change. |

## Infection Overlay Animation

Infection overlays are separate sprite layers composited on top of the base character:

| Tier | Overlay Behavior |
|------|-----------------|
| 1 (11-25) | Static overlay, subtle alpha breathing (0.25-0.35 alpha, 0.5 Hz) |
| 2 (26-50) | Slow growth animation (4 frames, 0.25 Hz loop) |
| 3 (51-75) | Active growth (6 frames, 0.5 Hz), occasional twitch |
| 4 (76-90) | Rapid pulse (8 frames, 1.0 Hz), emissive peaks |
| 5 (91-100) | Constant high-frequency pulse, particle emission from overlay edges |

Overlays do NOT follow character animation state. They are independent continuous loops rendered as a separate SpriteRenderer on a child GameObject.

## Fusion Entity Animation

| Rule | Detail |
|------|--------|
| Base frame | 192x192 (1.5x character size) |
| Composite assembly | Partner A silhouette visible in upper body, Partner B in lower body |
| Unique animations | Each of 12 fusion class combos gets Idle (6 frames) + full attack set |
| Mycelial tendrils | VFX Graph overlay connecting partner sprite regions, animated separately |
| Separation animation | 8 frames: entity splits, partners tumble apart, 0.5s invulnerability |

## Enemy Animation Guidelines

| Tier | Idle Complexity | Attack Telegraph | Death |
|------|----------------|-----------------|-------|
| Tier 1 | Simple (4 frames, slow) | 2-frame windup, fast strike | Instant dissolve (0.5s) |
| Tier 2 | Moderate (6 frames, personality) | 4-frame telegraph, clear tells | Dramatic dissolve (0.8s) |
| Tier 3 | Complex (8 frames, menacing) | 6-frame telegraph, screen shake on windup | Slow dissolve (1.2s) + loot burst |
| Boss | Elaborate (8+ frames, phase-dependent) | Phase-specific telegraphs, 10+ frame windup | Cinematic death (3s+) |

### Telegraph Requirement

All enemy attacks must have a visible telegraph of at least 15 frames (250ms at 60fps) before the damage frame. This is a hard gameplay requirement, not an artistic guideline.

## Implementation Notes

- Animations authored in Aseprite, exported as sprite sheets via custom pipeline script
- Unity Animator Controllers per entity type in `Assets/_Project/Animations/Controllers/`
- Sprite Atlas assets in `Assets/_Project/Art/Atlases/` grouped by entity + zone
- Animation events fire via `AnimationEventRelay` MonoBehaviour forwarding to ECS systems
- Input buffer implemented in `InputBufferSystem` (3-frame window = 50ms at 60fps)
- Infection overlays rendered by `InfectionOverlaySystem` reading `InfectionComponent` tier
- All frame timings validated by automated test: no attack faster than 250ms telegraph
