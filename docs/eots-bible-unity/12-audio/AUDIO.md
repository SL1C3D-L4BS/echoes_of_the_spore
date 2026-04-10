# 12 — AUDIO DIRECTION, NARRATION, VOIP & SIGNAL DESIGN

## Audio Stack
- Middleware: FMOD Studio
- Sample rate: 48 kHz
- Speaker mode: Stereo (2D game, no 3D spatialization)
- Buses: Master → Music, SFX, Ambience, Voice (VOIP), Narrator
- Narrator bus: immune to Infection DSP chain

## Music Design
- Adaptive per zone: each zone has a base track + combat layer + boss layer
- Combat layer fades in when enemies are alert, fades out 5s after last enemy dies
- Boss layer: unique composition per boss, phase transitions trigger musical shifts
- Hub (Canopy): evolves with Community Tree stage (5 variations)
- Key/BPM per zone specified in Section 03 (World Architecture)

## Spore-Talk DSP Chain
Signal path: Mic Capture → Opus Encode → Network → Opus Decode → Infection DSP → Proximity Mix → Output

### Infection DSP Parameters by Tier
| Tier | Pitch Shift | Resonance Filter | Word Replacement | Spore Mix |
|---|---|---|---|---|
| 0-10 | None | None | None | 0% |
| 11-25 | Echo (15ms delay, 0.2 feedback) | None | None | 0% |
| 26-50 | ±2 semitones (random, changes every 2-4s) | 800-2000 Hz sweep, 0.5 Hz LFO | None | 0% |
| 51-75 | -5 semitones (deep rumble) | Narrowband | 30% word replacement rate | 20% |
| 76-90 | Disabled — voice replaced with musical tones | Disabled | N/A (voice replaced) | 60% |
| 91-100 | Disabled | Disabled | N/A | 100% (pure spore ambience) |

### Word Replacement System (Tier 51-75)
- Detects word boundaries via silence gaps (>100ms between speech segments)
- Replaces random words with pre-recorded fungal terms (50-word dictionary)
- Does NOT require speech recognition — only silence detection
- Dictionary examples: "mycelium", "tendril", "substrate", "fruiting", "hyphae", "sporulate"

## SFX Design Principles
- Every player action has audio feedback within 1 frame
- Hit sounds pitch up slightly per combo hit (reinforces combo feel)
- Infection tier crossings trigger a unique audio sting (ascending for power, descending for communication loss)
- Boss phase transitions have dedicated stingers
- Mycelium Hunters have a unique ambient hum that grows louder with proximity (spatial audio cue in 2D via panning + volume)

## VOIP Technical Specs
- Codec: Opus, 24 kbps, 48 kHz mono
- Frame size: 20ms (960 samples)
- Jitter buffer: 3 frames (60ms)
- Packet format: type(1B) + player_id(4B) + seq(2B) + length(2B) + data(var) = ~256B max
- Transport: UDP, unreliable, sequence-ordered (drop stale packets)
- Threading: audio thread, never blocks game sim thread
- Infection level sync: via Redis, 500ms intervals (acceptable staleness for gradual DSP changes)
