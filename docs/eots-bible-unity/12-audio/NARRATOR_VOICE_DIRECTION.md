# Narrator Voice Direction

## The Mycelium Voice

The narrator is "The Mycelium" -- an ancient fungal intelligence that observes the
players and comments on their journey. It is not hostile, not benevolent. It is curious.

## Voice Character

| Attribute | Direction |
|-----------|-----------|
| Gender | Androgynous, slightly low register |
| Tone | Calm, measured, subtly amused |
| Pacing | Slow. Deliberate pauses between clauses (0.5-1.0s) |
| Emotion | Distant fascination, never urgency |
| Accent | Neutral (no strong regional identity) |
| Age feel | Ancient, timeless |
| Resonance | Slight chest resonance, warm |

## Recording Specs

| Parameter | Value |
|-----------|-------|
| Sample rate | 48 kHz |
| Bit depth | 24-bit |
| Format | WAV (source), Vorbis 80% (bank) |
| Mic | Large diaphragm condenser |
| Room | Treated booth, RT60 < 0.3s |
| Distance | 6-8 inches from mic |
| Pop filter | Required |
| Noise floor | < -60 dB |

## Line Categories

| Category | Count | Trigger | Priority |
|----------|-------|---------|----------|
| First Encounter | 50 | Player enters new zone first time | High |
| Boss Intro | 8 | Boss room entry | Highest |
| Boss Death | 8 | Boss defeated | Highest |
| Infection Commentary | 30 | Infection tier crossing | High |
| Fusion Observations | 20 | Fusion merge/separate | Medium |
| Death Remarks | 40 | Player death (contextual) | Medium |
| Community Tree | 15 | Tree stage changes | Low |
| Idle Musings | 60 | 60s of no significant action | Lowest |
| Lore Fragments | 100 | Collectible lore items | Medium |
| Spore Plan Reactions | 25 | Spore Plan executed | Medium |
| Tutorial Guidance | 30 | First-time mechanics | Highest |
| Run Milestones | 20 | Heat level changes, streak records | Low |
| **Total** | **~406** | — | — |

## Delivery Rules

| Rule | Specification |
|------|--------------|
| Max line duration | 8.0 seconds |
| Min gap between lines | 10.0 seconds |
| Max lines per minute | 2 |
| Never interrupt | Combat audio, VOIP, Spore Plan execution |
| Ducking | Music ducks -6 dB during narrator |
| Bus | Voice_Narrator (immune to Infection DSP) |
| Queue depth | 2 lines max (drop oldest if overflow) |
| Subtitles | Always available, synced to audio timestamps |

## Contextual Selection

Lines are weighted by context to avoid repetition and maintain relevance:

| Factor | Weight Modifier |
|--------|----------------|
| Never heard before | x3.0 |
| Heard once | x1.0 |
| Heard 2+ times | x0.2 |
| Matches current zone | x2.0 |
| Matches current infection tier | x1.5 |
| Player is fused | x1.5 (fusion lines) |
| Time since last line > 60s | x1.2 |

## Infection-Specific Direction

| Infection Tier | Narrator Tone Shift |
|---------------|-------------------|
| 0-25 | Observational, clinical curiosity |
| 26-50 | Warmer, encouraging transformation |
| 51-75 | Protective, territorial ("you are becoming us") |
| 76-90 | Ecstatic, triumphant |
| 91-100 | Silent (narrator stops; the player IS the mycelium now) |

## Death Lines Contextual Matrix

| Death Cause | Line Pool Size | Tone |
|------------|---------------|------|
| Boss kill | 8 | Respectful ("a worthy adversary") |
| Trash mob kill | 10 | Mildly disappointed |
| Environmental | 8 | Amused |
| Infection overload | 6 | Conflicted (wanted you to transform, not die) |
| Fall damage | 4 | Dry humor |
| Self-inflicted | 4 | Bewildered |

## Localization

| Language | VO Recorded | Subtitle Only |
|----------|------------|---------------|
| English | Yes | Yes |
| Japanese | Yes | Yes |
| French | Yes | Yes |
| German | Yes | Yes |
| Spanish | Yes | Yes |
| Portuguese (BR) | No | Yes |
| Korean | No | Yes |
| Simplified Chinese | No | Yes |
| Russian | No | Yes |
| Polish | No | Yes |
