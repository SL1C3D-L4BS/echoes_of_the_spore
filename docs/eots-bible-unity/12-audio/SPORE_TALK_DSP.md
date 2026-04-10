# Spore-Talk DSP Chain

## Signal Flow

```
Mic Input (48kHz mono)
  → Noise Gate (threshold: -40dB)
  → Opus Encode (24kbps, 20ms frames)
  → Network Transport (UDP, unreliable, sequenced)
  → Opus Decode (PLC for lost packets)
  → INFECTION DSP CHAIN ←── Infection Level (0-100)
    → Pitch Shift
    → Resonance Filter
    → Word Replacement
    → Spore Mix
  → Proximity Attenuation
  → Spatial Panning
  → Output Bus (Voice_VOIP)
```

## Infection DSP Tiers

### Tier 0-10: Clean
No DSP applied. Voice is natural.

### Tier 11-25: Echo
```
Effect: Delay
Parameters:
  delay_time: 15ms
  feedback: 0.2
  wet_mix: 0.3
Result: Subtle cavern echo, barely noticeable
```

### Tier 26-50: Pitch + Resonance
```
Effect: Random Pitch Shift + Resonance Sweep
Parameters:
  pitch_range: ±2 semitones (random per 500ms)
  resonance_center: 800-2000 Hz (LFO sweep)
  resonance_q: 2.0
  lfo_rate: 0.5 Hz
Result: Voice sounds unstable, alien, formant-shifted
```

### Tier 51-75: Word Replacement + Rumble
```
Effect: Pitch Down + Word Replacement + Spore Mix
Parameters:
  pitch_shift: -5 semitones (constant)
  word_replacement_rate: 30% of detected words
  spore_mix_level: 20%
  
Word Detection:
  - Silence threshold: -30dB for >100ms = word boundary
  - Replace random words with pre-recorded fungal terms
  - Dictionary: 50 words (mycelium, tendril, substrate, fruiting, 
    hyphae, sporulate, bloom, decay, spore, root, ...)
  - Replacement audio: 0.3-0.8s pre-recorded clips, pitch-matched
  
Result: Partially intelligible, punctuated by alien words
```

### Tier 76-90: Musical Tones
```
Effect: Voice replaced with musical tones
Parameters:
  voice_input: used as amplitude envelope only
  tone_generator: sine + saw blend
  tone_pitch: follows F0 (fundamental frequency) of voice
  chord_mode: minor triad based on detected pitch
  spore_mix_level: 60%
  
Result: Speaking produces musical tones, like a singing fungus
Comedy factor: HIGH (words become melody)
```

### Tier 91-100: Pure Spore
```
Effect: Voice completely replaced
Parameters:
  voice_bypass: true (original signal discarded)
  spore_ambience: 100% (pre-recorded fungal soundscape)
  amplitude_modulation: follows original voice envelope
  
Result: Player's mouth movements modulate ambient spore sounds
They can no longer communicate verbally with non-fused partners
Comedy factor: MAXIMUM
```

## Proximity Attenuation

```
volume = clamp(1.0 - (distance - 5.0) / 15.0, 0.0, 1.0)

Distance 0-5 units:   100% volume
Distance 5-20 units:  Linear falloff
Distance 20+ units:   0% (inaudible)
```

### Fusion Override
Fused partners hear each other at 100% volume regardless of proximity.
External listeners hear the fused entity's blended voice at the fused entity's position.

## Clip Mode

```csharp
public class ClipModeBuffer
{
    private CircularBuffer<AudioFrame> _rawBuffer;    // 30s of raw audio
    private CircularBuffer<AudioFrame> _dspBuffer;    // 30s of DSP'd audio
    
    public void SaveClip()
    {
        // Export both raw and processed as WAV
        // Add game watermark (SL1C3D-L4BS logo + Steam link)
        // Copy to clipboard as file path
    }
    
    public void ReprocessAtInfectionLevel(int level)
    {
        // Take raw buffer, apply DSP chain at specified level
        // Allows post-run replay of voice at any infection tier
    }
}
```

## Implementation Notes

- DSP runs on audio thread (never blocks sim thread)
- Infection level synced via Redis at 500ms intervals
- Opus PLC (Packet Loss Concealment) handles dropped voice packets
- All DSP parameters interpolate smoothly (no clicks/pops on tier transitions)
- Tier transitions take 2 seconds to fully crossfade
