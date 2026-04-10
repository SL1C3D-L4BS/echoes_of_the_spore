# Dialogue System -- Reactive Narration Architecture

## Philosophy

Echoes of the Spore has no traditional dialogue. No conversation trees, no NPC speech bubbles, no player dialogue choices. All narration is reactive -- the Mycelium Voice responds to player actions, not player words. The system is a one-way broadcast triggered by gameplay events.

## System Architecture

```
[Game Events] → [Trigger Evaluator] → [Priority Queue] → [Cooldown Gate] → [Audio Playback]
                                              ↓                                     ↓
                                       [Variant Selector]                  [Subtitle Display]
```

## Trigger System

### Event Sources

| Source | Example Events | Frequency |
|---|---|---|
| Combat System | Player death, boss kill, combo milestone | High |
| Infection System | Threshold crossed, Overload | Medium |
| Fusion System | Merge, separate, role swap | Medium |
| Exploration | Mural discovered, new room entered, rare item found | Low |
| Rite of Echoes | Score, kill, capture, match end | High (during Rite) |
| Community Tree | Branch unlocked, milestone reached | Low |
| Meta / Timer | Hub idle time > 60s, session time milestones | Very low |

### Trigger Evaluation

Each trigger is a struct:

```
TriggerDef {
    id: string,                    // e.g., "death_first"
    category: VoiceCategory,       // enum: Death, Fusion, Infection, etc.
    condition: Func<GameState, bool>, // predicate evaluated against current state
    priority: int,                 // 1 (highest) to 5 (lowest)
    cooldown_group: string,        // shared cooldown ID (e.g., "death")
    variants: int,                 // number of available audio variants
}
```

Evaluation order:
1. Event fires (e.g., `PlayerDeath`)
2. All triggers registered to that event type are evaluated
3. Conditions checked against current game state
4. Qualifying triggers sorted by priority
5. Highest priority trigger enters the queue

### Condition Examples

| Trigger | Condition |
|---|---|
| DEATH_FIRST | `player.total_deaths == 1` |
| DEATH_STREAK_3 | `player.deaths_last_5min >= 3` |
| HUBRIS_LOW_HP_ATTACK | `player.hp_percent < 0.1 AND player.last_action == Attack AND target.is_boss` |
| INFECT_50 | `player.infection >= 50 AND NOT player.heard_infect_50` |
| BOSS_INTRO | `boss.phase == 0 AND boss.combat_started_this_tick` |

## Priority Queue

| Parameter | Value |
|---|---|
| Queue depth | 2 (max 2 pending lines) |
| Overflow behavior | Lowest priority item dropped |
| Same priority tie-break | Most recent event wins |

### Priority Levels

| Priority | Categories | Override Rules |
|---|---|---|
| 1 | Boss Intros/Deaths | Interrupts any playing line. Ignores cooldown. |
| 2 | Death Commentary, Infection Milestones | Waits for current line to finish. Respects cooldown. |
| 3 | Hubris, Rare Events | Waits. Respects cooldown. Dropped if queue full. |
| 4 | Fusion, Rite Narration | Waits. Respects cooldown. Dropped if queue full. |
| 5 | Community Tree, Ambient | Only plays if queue empty and cooldown clear. |

## Cooldown System

### Global Cooldown

| Parameter | Value |
|---|---|
| Duration | 45 seconds |
| Starts | When a line begins playing |
| Applies to | All non-Priority-1 lines |
| Priority 1 behavior | Ignores global cooldown entirely |

### Category Cooldown

| Category | Category Cooldown | Stacks With Global |
|---|---|---|
| Death | 30s (separate from global) | Yes |
| Hubris | 60s | Yes |
| Infection | None (milestone-based, plays once) | Global only |
| Ambient | 120s | Yes |
| All others | None (use global only) | N/A |

### Cooldown Resolution

A line plays only if ALL of these are true:
1. Global cooldown has elapsed (45s since last line) OR trigger is Priority 1
2. Category cooldown has elapsed (if applicable)
3. Queue has space OR new trigger has higher priority than lowest queued item

## Variant Selection

### Algorithm

```
1. Get all variants for the trigger (e.g., death_first has 5 variants)
2. Filter out variants played this session (no repeats until all played)
3. Apply weights: unplayed variants have weight 1.0, recently played have weight 0.5
4. Select using weighted random
5. Mark selected variant as played this session
6. If all variants played this session, reset all to unplayed
```

### Variant Fatigue Prevention

| Rule | Detail |
|---|---|
| No immediate repeat | Same variant cannot play twice in a row for the same trigger |
| Session tracking | Per-session list of played variant indices per trigger |
| Cross-session | No tracking. Fresh start each delve. |
| Minimum variants | Every trigger must have at least 2 variants to prevent staleness |

## Interruption Rules

| Scenario | Behavior |
|---|---|
| Priority 1 arrives while line playing | Current line fades out (0.3s), Priority 1 plays |
| Priority 2+ arrives while line playing | Queued (if space). Plays after current line + cooldown. |
| Player enters loading screen | Current line continues playing (audio persists through load) |
| Player pauses game | Line pauses. Resumes on unpause. |
| Player dies during line | Line continues (death commentary may follow after cooldown) |
| Multiple simultaneous triggers | Highest priority wins. Others queued or dropped. |

## Subtitle System

| Parameter | Value |
|---|---|
| Display location | Bottom-center, above HUD |
| Font size | 24px default, adjustable in accessibility settings |
| Background | Semi-transparent black bar (75% opacity) |
| Duration | Matched to audio duration + 1s linger |
| Word timing | Karaoke-style highlight (word-by-word sync) |
| Speaker label | "The Mycelium" (always, no other speakers) |
| Accessibility | Subtitle-only mode available (for hearing-impaired or VO-off preference) |

## Multiplayer Behavior

| Scenario | Behavior |
|---|---|
| Solo player | Standard trigger evaluation |
| Co-op (2-4P) | Each player evaluates triggers independently on their client |
| Shared triggers | Boss Intros play for all players simultaneously (synced via server event) |
| Personal triggers | Death, Infection, Hubris play only for the affected player |
| Fusion triggers | Play for all fused partners |
| Rite narration | Plays for all match participants and spectators |

## Debug Tools

| Tool | Purpose |
|---|---|
| Trigger log | Lists all evaluated triggers per frame, with pass/fail reasons |
| Force-play command | `debug_voice play death_first 3` plays trigger variant directly |
| Cooldown display | Overlay showing global and category cooldown timers |
| Queue inspector | Shows current queue contents with priority and time-to-play |

## Implementation Notes

- `NarrationTriggerSystem` (ECS) evaluates triggers each tick, consuming events from `EventBus`
- `NarrationQueueSystem` manages the priority queue and cooldown timers
- `NarrationPlaybackSystem` controls AudioSource playback and subtitle display
- Trigger definitions loaded from `NarrationTriggerDatabase` ScriptableObject at session start
- Subtitle data loaded from localized string table with timing metadata
- Audio clips loaded via Addressables with `AsyncOperationHandle<AudioClip>` pre-warm on zone entry
- All narration state (cooldowns, variant history, queue) stored in `NarrationStateComponent` singleton
