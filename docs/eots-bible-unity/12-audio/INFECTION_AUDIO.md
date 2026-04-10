# Infection Audio Design

## Overview

Infection level (0-100) progressively transforms all audio the player hears.
The transformation is gradual, unsettling, and ultimately comedic at high tiers.

## Tier Audio Breakdown

### Tier 0-10: Clean Baseline

| Element | State |
|---------|-------|
| SFX | Normal |
| Music | Normal |
| VOIP | Clean pass-through |
| Ambience | Zone-standard |
| Narrator | Normal delivery |

### Tier 11-25: Subtle Unease

| Element | Modification | FMOD Parameter |
|---------|-------------|----------------|
| Ambience | +Spore whisper layer at -18 dB | `SporeWhisper = 0.15` |
| SFX | +2ms pre-delay on impacts | `InfImpactDelay = 2` |
| Music | No change | — |
| VOIP | Echo (15ms, 0.2 feedback) | See SPORE_TALK_DSP.md |

### Tier 26-50: Audible Corruption

| Element | Modification | FMOD Parameter |
|---------|-------------|----------------|
| Ambience | Spore whisper rises to -10 dB, adds pulsing | `SporeWhisper = 0.4` |
| SFX (player hits) | Wet organic layer blended at 20% | `OrganicBlend = 0.2` |
| SFX (footsteps) | Squelch layer added | `FootstepSquelch = 0.3` |
| Music | +5 cent detune on melodic instruments | `Detune = 5` |
| VOIP | Pitch shift + resonance sweep | See SPORE_TALK_DSP.md |

### Tier 51-75: Heavy Transformation

| Element | Modification | FMOD Parameter |
|---------|-------------|----------------|
| Ambience | Full spore soundscape at -6 dB | `SporeWhisper = 0.7` |
| SFX (player hits) | Organic layer at 50%, metallic reduced | `OrganicBlend = 0.5` |
| SFX (enemy death) | Replaced with spore burst sounds | Event swap |
| Music | Minor → diminished substitution | `ChordDarken = 0.6` |
| VOIP | Word replacement + rumble | See SPORE_TALK_DSP.md |
| Narrator | Lines shift to possessive tone | Script variant |

### Tier 76-90: Near-Total Takeover

| Element | Modification | FMOD Parameter |
|---------|-------------|----------------|
| Ambience | Dominant, pulsing mycelium hum | `SporeWhisper = 0.9` |
| SFX | All combat SFX gain organic overtones at 80% | `OrganicBlend = 0.8` |
| Music | Instruments become tonal drones | `DroneReplace = 0.7` |
| VOIP | Voice replaced with musical tones | See SPORE_TALK_DSP.md |
| Narrator | Ecstatic delivery | Script variant |

### Tier 91-100: Full Spore

| Element | Modification |
|---------|-------------|
| Ambience | Pure spore soundscape, no zone ambience |
| SFX | Fully organic, alien sound palette |
| Music | Replaced with generative spore drone |
| VOIP | Pure spore ambience, no voice |
| Narrator | Silent |

## Infection Stingers

Played on tier boundary crossings:

| Crossing | Direction | Sound | Duration | Priority |
|----------|----------|-------|----------|----------|
| 10 → 11 | Ascending | Low rumble + crack | 1.5s | High |
| 25 → 26 | Ascending | Wet growth burst | 1.5s | High |
| 50 → 51 | Ascending | Deep chord + spore exhale | 2.0s | Highest |
| 75 → 76 | Ascending | Triumphant fungal fanfare | 2.5s | Highest |
| 90 → 91 | Ascending | Silence, then full takeover | 3.0s | Highest |
| Any → lower | Descending | Receding, deflating | 1.0s | Medium |

## DSP Parameter Interpolation

All infection-driven parameters interpolate smoothly to prevent clicks:

| Parameter Type | Interpolation Time | Curve |
|---------------|-------------------|-------|
| Volume levels | 500ms | Linear |
| Pitch shift | 2000ms | Ease-in-out |
| Filter sweeps | 1000ms | Linear |
| Effect wet/dry | 500ms | Linear |
| Tier crossing stinger | Immediate trigger | — |
| Event swap (SFX replacement) | 200ms crossfade | Linear |

## Spore Ambience Layers

The spore soundscape is built from layered elements:

| Layer | Description | Volume at Tier 100 |
|-------|------------|-------------------|
| Base drone | Low frequency hum (80 Hz) | -6 dB |
| Mycelium pulse | Rhythmic organic throb, 0.5 Hz | -8 dB |
| Spore release | Random burst events, 2-5s intervals | -12 dB |
| Whisper chorus | Layered fungal whispers | -10 dB |
| Growth crackle | High-frequency organic crackle | -14 dB |

## Fusion Infection Audio

When fused, infection levels are averaged between partners.

| Scenario | Audio Behavior |
|----------|---------------|
| Both low (0-25) | Standard clean audio |
| Mixed (one high, one low) | Averaged tier, blend of both palettes |
| Both high (76+) | Amplified effect, unique fusion-specific tones |
| Separation after high tier | 2s reverb tail as audio "unsticks" |

## Implementation

```csharp
// InfectionAudioController.cs
public void UpdateInfectionAudio(int infectionLevel)
{
    float normalized = infectionLevel / 100f;
    _fmodMusicInstance.setParameterByName("InfectionLevel", normalized);
    _fmodAmbienceInstance.setParameterByName("SporeWhisper", 
        ComputeSporeWhisperLevel(infectionLevel));
    _sfxOrganicBlend = ComputeOrganicBlend(infectionLevel);
}
```
