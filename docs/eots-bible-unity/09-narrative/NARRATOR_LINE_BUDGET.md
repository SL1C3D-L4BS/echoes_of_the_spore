# Narrator Line Budget

## Budget Summary

| Metric | Value |
|---|---|
| Total triggers | 233 |
| Average variants per trigger | ~3.9 |
| Total lines (base) | ~898 |
| Recording target (minimum) | 900 lines |
| Stretch goal | 1,200 lines (additional variants + post-vote content) |
| Estimated recording time | 4-5 hours (studio time) |
| Estimated final audio | ~75 minutes (at avg 5s per line) |

## Detailed Breakdown

| Category | Triggers | Variants | Total | Avg Duration | Category Audio |
|---|---|---|---|---|---|
| Death Commentary | 40 | 5 | 200 | 4s | 13.3 min |
| Fusion Commentary | 25 | 4 | 100 | 5s | 8.3 min |
| Infection Milestones | 12 | 5 | 60 | 6s | 6.0 min |
| Community Tree | 20 | 3 | 60 | 5s | 5.0 min |
| Hubris Moments | 30 | 5 | 150 | 4s | 10.0 min |
| Rare Events | 20 | 4 | 80 | 6s | 8.0 min |
| Boss Intros/Deaths | 16 | 3 | 48 | 8s | 6.4 min |
| Rite Narration | 30 | 4 | 120 | 5s | 10.0 min |
| Ambient (Hub) | 40 | 2 | 80 | 5s | 6.7 min |
| **Total** | **233** | -- | **898** | 5s avg | **~74 min** |

### Stretch Goal Additional Lines

| Category | Additional Triggers | Variants | Additional Lines |
|---|---|---|---|
| Post-FEED world lines | 30 | 3 | 90 |
| Post-STARVE world lines | 30 | 3 | 90 |
| New Game+ commentary | 20 | 3 | 60 |
| Seasonal event lines | 20 | 3 | 60 |
| **Stretch Total** | **100** | -- | **300** |

**Grand total with stretch:** ~1,198 lines.

## Recording Budget

### Studio Time

| Session | Duration | Content | Cost Estimate |
|---|---|---|---|
| Session 1 | 4 hours | Death (200), Fusion (100), Infection (60) | $2,400 |
| Session 2 | 4 hours | Hubris (150), Boss (48), Rare (80) | $2,400 |
| Session 3 | 3 hours | Rite (120), Community (60), Ambient (80) | $1,800 |
| Session 4 (stretch) | 4 hours | Post-vote (180), NG+ (60), Seasonal (60) | $2,400 |
| Pickup session | 2 hours | Re-records, additions, fixes | $1,200 |
| **Total** | **17 hours** | **~1,200 lines** | **~$10,200** |

Cost assumptions: $600/hr studio + talent combined. Adjust per market.

### Post-Production

| Task | Hours | Cost Estimate |
|---|---|---|
| Edit & cleanup (per line) | 0.02h (avg) | $3,600 (1,200 lines) |
| Layering (whisper + sub-harmonic) | 0.03h | $5,400 |
| Mastering | 8h | $1,600 |
| Implementation & testing | 16h | $3,200 |
| **Post-Production Total** | -- | **~$13,800** |

### Total VO Budget

| Component | Cost |
|---|---|
| Studio recording | $10,200 |
| Post-production | $13,800 |
| **Total** | **~$24,000** |

## VO Direction

### Character Brief

| Attribute | Direction |
|---|---|
| Identity | The Mycelium. Ancient fungal consciousness. Not a person. Not a god. A network that thinks. |
| Gender presentation | Ambiguous. Not androgynous -- beyond gender. Deep register with harmonic resonance. |
| Emotion range | Amused to solemn. Never angry. Never pleading (except post-STARVE lines). Never afraid. |
| Speech pattern | Measured. Deliberate. Pauses between phrases. No contractions. No slang. |
| Relationship to player | Gardener to plant. Parent to child. Scientist to subject. All three simultaneously. |
| Awareness | Knows it is in a game? Ambiguous. Some lines could be read as fourth-wall-aware. Never confirmed. |

### Delivery Notes Per Category

| Category | Direction |
|---|---|
| Death Commentary | Range from warm welcome (first death) to cutting mockery (streak deaths). Never cruel without wit. |
| Fusion Commentary | Fascinated. Two becoming one echoes the Progenitor's nature. Genuine interest. |
| Infection Milestones | Increasingly intimate. Early lines are observational. Late lines are possessive, tender. |
| Hubris Moments | Sharp. Quick delivery (contrast with usual slow pace). The Voice enjoys hubris. |
| Boss Intros | Solemn. These were its children too. Introducing them with a mix of pride and sadness. |
| Boss Deaths | Relief mixed with grief. "It is done. They can rest." Not celebratory. |
| Rite Narration | Energized. Sports-commentator energy filtered through ancient wisdom. The Voice enjoys the Rite. |
| Ambient (Hub) | Contemplative. Philosophical. Observations about the nature of growth, decay, memory. |

### Casting Notes

| Requirement | Detail |
|---|---|
| Voice type | Bass-baritone preferred. Rich overtones. Capable of whisper-to-projection range. |
| Acting range | Stage-trained preferred. Comfort with non-human characters. |
| Stamina | 4-hour sessions. Volume and tone consistency critical. |
| Reference performances | The Narrator (Bastion), Rucks. The Darkness (Darkest Dungeon), Wayne June. The Observer (Outer Wilds DLC). |
| Audition material | 5 lines from Death Commentary (range of tones) + 3 lines from Infection Milestones (intimacy test) |

## File Naming Convention

```
mv_{category}_{trigger_id}_{variant}.wav
```

Examples:
- `mv_death_first_01.wav`
- `mv_hubris_solo_apex_03.wav`
- `mv_boss_intro_smelter_02.wav`

## Localization Plan

| Phase | Languages | Approach |
|---|---|---|
| Launch | English | Full VO |
| Launch | FIGS, JPN, KOR, ZHS, ZHT | Subtitles only |
| Post-launch update 1 | French, German, Japanese | Full VO (new cast per language) |
| Post-launch update 2 | Spanish, Italian, Korean | Full VO |
| Community | Other languages | Community subtitle contributions |

## Implementation Notes

- Audio files stored as Addressable assets in `Assets/Audio/VO/Mycelium/` grouped by category
- Loaded on-demand per zone (only relevant categories pre-loaded)
- Memory budget: ~20 MB for loaded VO clips (compressed Vorbis, quality 5)
- Subtitle timing stored in `SubtitleTimingAsset` ScriptableObject per line (word-level timestamps for karaoke-style display)
- Recording session tracked in production spreadsheet with columns: line_id, take_count, selected_take, notes, approved
