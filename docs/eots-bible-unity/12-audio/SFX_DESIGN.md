# SFX Design

## Design Philosophy

Every player action produces audio feedback within 1 frame (16.67ms). SFX reinforce
game feel: hits feel heavy, movement feels responsive, infection feels invasive.

## SFX Categories and Voice Limits

| Category | Max Voices | Priority | Steal Behavior |
|----------|-----------|----------|----------------|
| Player Actions | 8 | Highest | Oldest |
| Combat Impacts | 12 | High | Quietest |
| Enemy Sounds | 8 | Medium | Furthest |
| Environment | 6 | Low | Quietest |
| UI | 4 | Highest | None (queue) |
| Infection Stingers | 2 | Highest | None (always play) |

## Player Action SFX

| Action | Event Path | Variations | Pitch Range | Notes |
|--------|-----------|-----------|-------------|-------|
| Light Attack | Player/Attack_Light | 4 | 0.95-1.05 | Random round-robin |
| Heavy Attack | Player/Attack_Heavy | 3 | 0.90-1.00 | Slower, deeper |
| Dash | Player/Dash | 2 | 1.0 | Whoosh + cloth |
| Air Attack | Player/Attack_Air | 3 | 1.0-1.1 | Higher pitch than ground |
| Jump | Player/Jump | 2 | 1.0-1.05 | Soft launch sound |
| Land | Player/Land | 3 | 0.95-1.0 | Based on fall height |
| Wall Slide | Player/WallSlide | 1 | 1.0 | Looping, velocity-scaled |
| Heal (Bind) | Player/Heal_Bind | 1 | 1.0 | Organic, wet growth |
| Heal (Weaver) | Player/Heal_Weaver | 1 | 1.0 | Crystalline chime |
| Death | Player/Death | 3 | 0.9-1.0 | Heavy, final |

## Combat Impact SFX

| Event | Trigger | Combo Pitch Formula | Additional |
|-------|---------|-------------------|------------|
| Hit_Deal | Damage dealt | `1.0 + (combo * 0.02)` cap 1.6 | Reverb shortens with combo |
| Hit_Receive | Damage taken | 0.85 fixed | Screen shake sync |
| CriticalHit | Crit triggers | Base + 0.3 | Flash + ring mod layer |
| Hitstop | Hitstop frame | None (single crack) | Timed to hitstop duration |
| ComboTick | Combo counter +1 | Ascending scale | Musical note (C4 + combo) |
| Block | Shield block | 0.9 | Metallic clang |
| Parry | Perfect parry | 1.2 | Bright metallic ring |

## Enemy SFX Table

| Enemy Type | Idle Loop | Alert | Attack | Death | Proximity Cue |
|-----------|-----------|-------|--------|-------|---------------|
| Sporelings | Soft chittering | Screech | Splat | Pop | None |
| Mycelium Hunters | Low hum | Hum intensifies | Tendril whip | Dissonant chord | Hum volume by distance |
| Rot Knights | Armor creak | Battle cry | Sword clang | Collapse + echo | Footsteps |
| Sporeweavers | Whisper | Chant | Magic burst | Dissolve | Whisper panning |
| Zone Bosses | Unique per boss | Unique stinger | Phase-specific | Unique death | Unique ambience |

## Environment SFX

| Source | Type | Behavior |
|--------|------|----------|
| Acid Pool | Loop | Volume by proximity, pitch by pool size |
| Spike Trap | One-shot | Trigger on activation |
| Fire Vent | Loop + burst | Loop ambient, burst on fire cycle |
| Mycelium Door | One-shot | Open/close variants |
| Collectible Pickup | One-shot | Pitch up per rapid pickup |
| Echo Deposit | One-shot | Resonant chime, unique per tier |
| Community Tree | Loop | Evolves with tree stage |

## Spore Plan SFX

| Plan Type | Charge Sound | Execution Sound | Duration |
|-----------|-------------|-----------------|----------|
| Offensive Burst | Rising oscillator | Explosion + reverb | 0.5s + 2.0s tail |
| Defensive Shield | Crystal formation | Sustained hum | 1.0s + loop |
| Terrain Reshape | Earth rumble | Rock shift cascade | 1.5s |
| Healing Bloom | Organic growth | Chime cascade | 1.0s |

## Technical Specs

| Parameter | Value |
|-----------|-------|
| Sample rate | 48 kHz |
| Bit depth | 16-bit (SFX), 24-bit (music) |
| Format (source) | WAV |
| Format (bank) | Vorbis (SFX), FADPCM (music) |
| Vorbis quality | 60% (SFX), 80% (ambience) |
| Max one-shot duration | 3.0s |
| Max loop duration | 30.0s (streams beyond) |
| Polyphony limit | 64 total voices |
