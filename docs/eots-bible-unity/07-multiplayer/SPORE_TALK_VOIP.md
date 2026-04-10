# Spore-Talk -- Proximity VOIP Specification

## Overview

Spore-Talk is the in-game voice communication system. It uses proximity-based spatial audio with a unique infection-tier DSP chain that progressively distorts player voices as their Infection level rises.

## Codec and Transport

| Parameter | Value |
|---|---|
| Codec | Opus |
| Bitrate | 24 kbps (mono) |
| Sample rate | 48 kHz |
| Frame size | 20ms |
| Channels | 1 (mono, spatialized by client) |
| Transport | UDP, piggybacked on game data channel |
| Packet size | ~60 bytes per frame (header + payload) |
| Jitter buffer | 80ms (4 frames) |

## Proximity Model

| Distance (units) | Volume |
|---|---|
| 0 - 5 | 100% |
| 5 - 10 | Linear falloff: 100% to 60% |
| 10 - 15 | Linear falloff: 60% to 20% |
| 15 - 20 | Linear falloff: 20% to 0% |
| 20+ | 0% (inaudible) |

- Falloff is per-ear (stereo panning based on relative position)
- Occlusion: walls reduce volume by 40% per wall between speaker and listener
- Max occlusion: 80% reduction (voice always slightly audible through 2+ walls at close range)

## Spatial Audio

- HRTF spatialization using Unity's built-in spatial audio
- Voice source attached to player head bone
- Doppler effect: disabled (unnatural for voice)
- Reverb: zone-specific reverb send (caves echo, open areas dry)
- Vertical attenuation: same as horizontal (no special Y-axis handling)

## Infection DSP Chain

Audio passes through four DSP stages in order. Each stage intensity scales with the speaker's Infection level.

### DSP Pipeline

```
Raw Mic → Noise Gate → [Pitch Shift] → [Resonance Filter] → [Word Replacement] → [Spore Mix] → Spatial Output
```

### Stage 1: Pitch Shift

| Infection Range | Pitch Multiplier |
|---|---|
| 0 - 24 | 1.0 (no shift) |
| 25 - 49 | 0.95 (subtle deepening) |
| 50 - 74 | 0.85 (noticeable drop) |
| 75 - 89 | 0.70 (distorted, rumbling) |
| 90 - 100 | 0.55 (barely human) |

### Stage 2: Resonance Filter

| Infection Range | Resonance Q | Center Freq |
|---|---|---|
| 0 - 24 | Bypass | -- |
| 25 - 49 | 2.0 | 800 Hz |
| 50 - 74 | 4.0 | 600 Hz |
| 75 - 89 | 6.0 | 400 Hz |
| 90 - 100 | 10.0 | 250 Hz |

Adds a hollow, fungal quality to the voice.

### Stage 3: Word Replacement

| Infection Range | Replacement Rate | Replacement Source |
|---|---|---|
| 0 - 49 | 0% | -- |
| 50 - 64 | 10% of words | Whispered fungal syllables |
| 65 - 79 | 25% of words | Spore-language fragments |
| 80 - 89 | 50% of words | Full spore-language substitution |
| 90 - 100 | 80% of words | Near-complete spore-language |

Word replacement uses silence detection to identify word boundaries, then substitutes pre-recorded spore-language samples of matching duration.

### Stage 4: Spore Mix

| Infection Range | Mix Level | Effect |
|---|---|---|
| 0 - 24 | 0% | Clean voice |
| 25 - 49 | 10% | Faint spore ambience underlayer |
| 50 - 74 | 25% | Audible spore hum beneath voice |
| 75 - 89 | 45% | Voice and spore sounds nearly equal |
| 90 - 100 | 65% | Spore ambience dominates; voice is undertone |

Spore Mix blends a procedural spore ambience track (generated from noise + granular synthesis) with the processed voice.

## Fusion Override

- Fused partners hear each other at 100% volume, 0% spatialization (centered audio)
- DSP chain still applies (infection effects remain)
- External listeners hear the fused entity's voice at the fused entity's position
- External voice is a blend of all fused partners' processed audio (equal mix)

## Clip Mode

| Parameter | Value |
|---|---|
| Buffer length | 30 seconds (rolling) |
| Storage | Raw (pre-DSP) + processed (post-DSP) saved as separate WAV files |
| Trigger | Dedicated keybind (default: F9) |
| File location | `{save_dir}/clips/{session_id}_{timestamp}.wav` |
| Max clips per session | 20 |

### Infection Replay

- Post-run feature: load any saved clip and re-process through DSP chain at any infection level
- Useful for hearing what you sounded like to others at different infection tiers
- Accessible from the post-run summary screen

## Noise System Integration

- Active Spore-Talk microphone generates noise value of 50
- Noise applies to the speaker's position for AI detection
- In Fusion, noise is generated at the fused entity's position
- Push-to-talk releases generate 0 noise; open mic silence detection prevents noise when not speaking

## Voice Settings

| Setting | Default | Options |
|---|---|---|
| Voice mode | Push-to-talk | Push-to-talk, Open mic |
| Mic sensitivity | -40 dBFS | -60 to -20 dBFS |
| Voice volume | 80% | 0-100% |
| Spatial audio | On | On, Off (centered) |
| Self-monitoring | Off | Off, Low, Medium |
| DSP preview | Off | Hear your own DSP-processed voice |

## Implementation Notes

- Unity: custom AudioSource per player with `OnAudioFilterRead` for DSP chain
- Opus encoding/decoding via native plugin (libopus)
- Jitter buffer implemented as ring buffer with interpolation on underrun
- Word replacement uses Voice Activity Detection (WebRTC VAD) for boundary detection
- Spore-language samples: 200 pre-recorded syllables, pitch-matched at runtime
- Clip Mode uses a circular buffer writing to temp file; on trigger, flush to permanent storage
