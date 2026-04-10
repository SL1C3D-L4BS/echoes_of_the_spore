# Boss Design Template

## Overview

Every boss in Echoes follows a three-phase structure. Phase 1 teaches, Phase 2 escalates, Phase 3 introduces desperation mechanics. This template defines the framework all bosses must conform to. Individual boss specs reference this document.

## Three-Phase Structure

| Phase | HP Range | Design Goal | Tempo | New Mechanics |
|-------|----------|-------------|-------|---------------|
| Phase 1 | 100%-66% | Teach core patterns | Moderate, rhythmic | 2-3 attacks, 1 arena mechanic |
| Phase 2 | 66%-33% | Escalate and combine | Aggressive, less downtime | 1-2 new attacks, pattern combos |
| Phase 3 | 33%-0% | Desperation, all-in | Relentless, high pressure | 1 signature move, arena transforms |

### Phase Transition Events

| Transition | Trigger | Duration | Player State |
|------------|---------|----------|-------------|
| Phase 1 to 2 | Boss HP reaches 66% | 2.0 seconds | i-frames (30 frames), repositioned to arena center |
| Phase 2 to 3 | Boss HP reaches 33% | 3.0 seconds | i-frames (45 frames), repositioned |
| Hitstop on transition | 12 frames (200 ms) | Included in above | Dramatic pause before cutaway |

### Heat Modifier: The Rot Ascends

When The Rot Ascends (Heat condition) is active, Phase 3 begins at 50% HP instead of 33%. Phase 2 range becomes 66%-50%.

## Attack Pattern Design Rules

| Rule | Specification |
|------|---------------|
| Minimum telegraph | 12 frames (200 ms) for fast attacks, 30 frames (500 ms) for heavy |
| Maximum concurrent threats | 2 in Phase 1, 3 in Phase 2, 4 in Phase 3 |
| Safe window after combo | Minimum 1.5 seconds between boss attack sequences |
| Punish window per phase | Phase 1: 2.0s, Phase 2: 1.5s, Phase 3: 1.0s |
| Pattern randomization | Weighted random from pool; no identical back-to-back patterns |
| Co-op scaling | +40% HP per additional player, no new mechanics |

## Attack Template

Each boss attack must define:

| Field | Description | Example |
|-------|-------------|---------|
| Name | Internal identifier | `lithic_guardian_slam` |
| Telegraph | Visual/audio warning | Ground glow (500 ms) + rumble SFX |
| Startup | Frames before hitbox active | 18 frames |
| Active | Frames hitbox is live | 6 frames |
| Recovery | Frames before next action | 24 frames |
| Damage | Base damage value | 4 HP (out of player's ~12-20) |
| Hitbox | Shape and size | Rectangle: 4 tiles wide, 2 tiles tall |
| Avoidance | Intended dodge method | Jump or dash through |
| Tracking | Does it track player? | Yes during startup, locks on active |

## Arena Design Requirements

| Parameter | Specification |
|-----------|---------------|
| Minimum size | 16 x 10 tiles (width x height) |
| Maximum size | 24 x 14 tiles |
| Platform count | 2-4 platforms (varied heights) |
| Hazard zones (Phase 1) | 0-1 (optional environmental danger) |
| Hazard zones (Phase 2) | 1-2 (arena constricts or adds danger) |
| Hazard zones (Phase 3) | 2-3 (arena actively hostile) |
| Safe zones | Always at least 1 safe platform reachable |
| Wall interaction | Bosses cannot be cornered; they teleport/phase at arena edges |

### Arena Transformation Examples

| Phase | Transformation Type | Effect |
|-------|-------------------|--------|
| Phase 2 | Floor collapse | 2 tiles of floor become pits |
| Phase 2 | Environmental hazard | Acid/fire/spore clouds at edges |
| Phase 3 | Platform destruction | 1-2 platforms break |
| Phase 3 | Full arena shift | Entire arena changes (e.g., flooding, rotation) |

## Camera Behavior During Boss Fights

| Event | Camera Action | Timing |
|-------|--------------|--------|
| Boss intro | Cinematic pan to boss, name card | 3 seconds, skippable |
| Phase transition | Slow zoom to boss center (10%) | During transition window |
| Signature attack (Phase 3) | Pull back 15% for wider view | Attack telegraph start |
| Boss death | Freeze frame on final hit (0.5s), zoom in | 2 seconds total |
| Return to gameplay | Smooth return to default framing | 1 second |

## Music Design Per Phase

| Phase | Music Behavior | Intensity | Layer Changes |
|-------|---------------|-----------|---------------|
| Phase 1 | Base track, moderate tempo | 1.0x | Percussion light, melody prominent |
| Phase 2 | Transition sting, tempo +15% | 1.3x | Heavy percussion added, harmony shifts minor |
| Phase 3 | Transition sting (dramatic), tempo +25% | 1.6x | Full orchestra, choir/synth layer, bass intensifies |
| Boss death | Music cuts to single sustained note | Fade | 2-second decay into silence |

Music transitions use 1-bar crossfades synced to the nearest downbeat.

## Boss HP Scaling (Multiplayer)

| Player Count | HP Multiplier | Notes |
|-------------|---------------|-------|
| 1 (solo) | 1.0x | Baseline |
| 2 | 1.4x | Standard co-op |
| 3 | 1.8x | Scaled for DPS increase |
| 4 | 2.2x | Full party |
| 5 (Fused) | 2.5x | Hyper-Fusion encounters |

## Loot and Reward Structure

| Drop | Condition | Quantity |
|------|-----------|----------|
| Echoes | Always | Base 200-500 (x Heat multiplier) |
| Echo Boon offering | Always (first kill) | 1 Heroic-tier choice |
| Crest fragment | First kill only | 1 per boss (12 total across game) |
| Cosmetic drop | Heat 10+ kill | Boss-specific weapon skin |
| Codex entry | Auto-unlock | Boss bestiary entry |

## Boss Checklist (For Designers)

- [ ] Phase 1 has 2-3 learnable attacks with clear telegraphs
- [ ] Phase 2 introduces 1-2 new attacks and combines Phase 1 patterns
- [ ] Phase 3 has a signature desperation move with arena transformation
- [ ] All attacks have defined frame data (startup/active/recovery)
- [ ] Arena has platforms, hazards scale per phase, and always 1 safe zone
- [ ] Music transitions are defined for all phase boundaries
- [ ] Camera behavior is scripted for intro, transitions, signature, and death
- [ ] Co-op HP scaling tested at all player counts
- [ ] Heat modifier (Rot Ascends) Phase 3 threshold tested
- [ ] Codex entry and loot table defined
