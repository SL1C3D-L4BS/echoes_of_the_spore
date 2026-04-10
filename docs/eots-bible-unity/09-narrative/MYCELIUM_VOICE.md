# The Mycelium Voice -- Reactive Narration System

## Overview

The Mycelium Voice is the game's narrator -- an omnipresent fungal consciousness that comments on player actions, deaths, discoveries, and pivotal moments. It speaks with detached amusement, ancient wisdom, and occasional cruelty. There are no traditional dialogue trees; the Voice reacts.

## Line Budget Summary

| Category | Triggers | Variants/Trigger | Total Lines |
|---|---|---|---|
| Death Commentary | 40 | 5 | 200 |
| Fusion Commentary | 25 | 4 | 100 |
| Infection Milestones | 12 | 5 | 60 |
| Community Tree Unlocks | 20 | 3 | 60 |
| Hubris Moments | 30 | 5 | 150 |
| Rare Events | 20 | 4 | 80 |
| Boss Intros/Deaths | 16 | 3 | 48 |
| Rite Narration | 30 | 4 | 120 |
| Ambient (Hub) | 40 | 2 | 80 |
| **Total** | **233** | -- | **~898** |

Recording target: 900 lines minimum. Stretch goal: 1,200 lines (additional variants).

## Trigger Categories (Detailed)

### Death Commentary (40 triggers, 5 variants each)

| Trigger ID | Condition | Tone |
|---|---|---|
| DEATH_FIRST | Player's first death ever | Welcoming, warm |
| DEATH_BOSS | Killed by a zone boss | Impressed, respectful |
| DEATH_FALL | Death by falling | Amused |
| DEATH_SELF | Killed by own explosion/reflect | Delighted |
| DEATH_HUNTER | Killed by Mycelium Hunter | Approving, parental |
| DEATH_STREAK_3 | 3 deaths in 5 minutes | Pitying |
| DEATH_STREAK_5 | 5 deaths in 10 minutes | Mocking |
| DEATH_INFECTION | Infection Overload death | Tender, intimate |
| DEATH_TEAM_WIPE | All party members dead | Solemn |
| DEATH_FINAL_BOSS | Killed by Mycelium Heart | Disappointed |
| ... | (30 more contextual triggers) | Varied |

### Hubris Moments (30 triggers, 5 variants each)

Triggered when a player does something overconfident:

| Trigger ID | Condition |
|---|---|
| HUBRIS_LOW_HP_ATTACK | Attacking a boss at <10% HP instead of healing |
| HUBRIS_IGNORE_ADDS | Ignoring add spawns for 15+ seconds |
| HUBRIS_SOLO_APEX | Engaging Tier 3 alone when party is available |
| HUBRIS_HIGH_INFECTION_PUSH | Entering a new room above Infection 80 |
| HUBRIS_SKIP_BOON | Declining a boon offering |
| HUBRIS_DASH_INTO_BOSS | Dashing directly into a boss during attack telegraph |
| ... | (24 more) |

### Infection Milestones (12 triggers, 5 variants each)

| Trigger ID | Infection Level | Tone |
|---|---|---|
| INFECT_10 | 10 | Curious, noting the change |
| INFECT_25 | 25 | Pleased, encouraging |
| INFECT_40 | 40 | Proud, possessive |
| INFECT_50 | 50 | Midpoint acknowledgment, shift in tone |
| INFECT_60 | 60 | Growing intimacy |
| INFECT_70 | 70 | Dominance, ownership |
| INFECT_75 | 75 | Warning from the Voice about losing self |
| INFECT_80 | 80 | Celebration |
| INFECT_85 | 85 | Urgency, desire |
| INFECT_90 | 90 | Near-complete merger narrative |
| INFECT_95 | 95 | Final plea/invitation |
| INFECT_100 | 100 | Overload trigger (not a line, triggers sequence) |

## Cooldown and Priority

| Parameter | Value |
|---|---|
| Global cooldown | 45 seconds between any Mycelium Voice lines |
| Priority override | Boss Intros ignore cooldown (always play) |
| Queue depth | Max 2 lines queued. If queue full, lowest priority dropped. |
| Interrupt rules | Boss Intro interrupts any playing line. Nothing else interrupts. |

### Priority Levels

| Priority | Categories |
|---|---|
| 1 (highest) | Boss Intros/Deaths |
| 2 | Death Commentary, Infection Milestones |
| 3 | Hubris Moments, Rare Events |
| 4 | Fusion Commentary, Rite Narration |
| 5 (lowest) | Community Tree Unlocks, Ambient |

## Audio Bus Routing

```
[Mycelium Voice Source] → [VO Bus] → [Master Bus] → [Output]
                              ↓
                    NOT routed through Spore-Talk DSP
```

- The Mycelium Voice is immune to all DSP effects (Infection, proximity, etc.)
- Always plays at full clarity regardless of player Infection level
- Spatialization: non-spatial (centered, equal volume in both ears)
- Volume: fixed at 90% of VO bus (player can adjust VO bus, not individual Voice volume)
- Reverb: zone-matched send (light reverb in caves, dry in open areas)

## Variant Selection

When a trigger fires:
1. Check cooldown (45s since last line). If on cooldown, queue or drop.
2. Select variant using weighted random (each variant has a weight, initially equal).
3. After playing a variant, its weight is halved (reduces repeat chance).
4. Weights reset to equal when all variants of a trigger have been played.
5. Per-session tracking: no variant repeats until all variants for that trigger have played.

## Tone Guidelines

| Tone | Description | Used For |
|---|---|---|
| Amused | Detached, watching with entertainment | Deaths, failures |
| Tender | Intimate, almost loving | Infection progression |
| Proud | Parental approval | Player achievements, fusion |
| Mocking | Pointed, cruel | Repeated failures, hubris |
| Solemn | Serious, weight of history | Lore moments, boss intros |
| Cryptic | Layered meaning, hints at deeper truth | Rare events, murals |
| Urgent | Rare, breaks character | True Ending vote, Overload |

## Recording Notes

| Parameter | Specification |
|---|---|
| Casting | Single voice actor. Gender-ambiguous delivery. Deep register with resonance. |
| Processing | Layered: primary recording + whispered duplicate + pitch-shifted sub-harmonic |
| Line length | 3-12 seconds average. Max 20 seconds (Boss Intros only). |
| Breath | No audible breaths. Edit out or suppress. |
| Pacing | Slow, deliberate. 2-word minimum pause between clauses. |
| Direction note | The Voice is not evil. It is ancient, patient, and genuinely fond of the player in the way a gardener is fond of a particularly interesting plant. |

## Localization

| Language | Approach |
|---|---|
| English | Primary recording. All 900+ lines. |
| FIGS + CJK | Subtitles only at launch. VO localization in post-launch update. |
| Subtitle timing | Synced to English VO duration. Scaled for reading speed per locale. |

## Implementation Notes

- `MyceliumVoiceSystem` listens to game events via `EventBus<T>` pattern
- Trigger evaluation runs each tick; filtered by cooldown in `VoiceCooldownComponent`
- Variant selection uses `WeightedRandomSelector` with per-session state
- Audio playback via dedicated `AudioSource` on a persistent `MyceliumVoice` GameObject (DontDestroyOnLoad)
- Queue implemented as `PriorityQueue<VoiceLine>` with max size 2
- Localized subtitle keys follow pattern: `mv_{trigger_id}_{variant_index}`
