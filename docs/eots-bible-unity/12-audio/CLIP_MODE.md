# Clip Mode

## Overview

Clip Mode is a built-in recording feature that captures the last 30 seconds of audio
(both raw microphone input and DSP-processed output) for social sharing. Players can
save hilarious Spore-Talk moments, boss kills, or comedic deaths.

## Buffer Architecture

```
Continuous Recording (always running):
  Raw Mic Buffer ─────── CircularBuffer<float[]> ─── 30s @ 48kHz mono
  DSP Output Buffer ──── CircularBuffer<float[]> ─── 30s @ 48kHz mono
  Game Audio Buffer ──── CircularBuffer<float[]> ─── 30s @ 48kHz stereo

On Save Trigger:
  -> Freeze buffers (copy to export)
  -> Mix selected tracks
  -> Encode to format
  -> Copy to clipboard / save to disk
```

## Buffer Specifications

| Buffer | Sample Rate | Channels | Duration | Memory |
|--------|-----------|----------|----------|--------|
| Raw Mic | 48000 Hz | 1 (mono) | 30s | 2.88 MB |
| DSP Output | 48000 Hz | 1 (mono) | 30s | 2.88 MB |
| Game Audio | 48000 Hz | 2 (stereo) | 30s | 5.76 MB |
| **Total** | — | — | — | **11.52 MB** |

## Clip Export Options

| Option | Format | Quality | File Size (30s) |
|--------|--------|---------|----------------|
| Raw Voice Only | WAV 48kHz/16-bit | Lossless | ~2.7 MB |
| DSP Voice Only | WAV 48kHz/16-bit | Lossless | ~2.7 MB |
| Full Mix | OGG Vorbis Q6 | High | ~1.2 MB |
| Social Share | OGG Vorbis Q4 | Medium | ~0.6 MB |
| Side-by-Side | WAV stereo (raw L, DSP R) | Lossless | ~5.5 MB |

## Reprocessing System

Players can replay their raw voice through any infection tier's DSP chain:

```csharp
public class ClipReprocessor
{
    public AudioClip ReprocessAtTier(float[] rawSamples, int infectionTier)
    {
        var dsp = new InfectionDSPChain(infectionTier);
        var output = new float[rawSamples.Length];
        
        for (int i = 0; i < rawSamples.Length; i += FRAME_SIZE)
        {
            var frame = rawSamples[i..(i + FRAME_SIZE)];
            output[i..(i + FRAME_SIZE)] = dsp.Process(frame);
        }
        
        return CreateAudioClip(output, 48000, 1);
    }
}
```

| Reprocess Preset | Infection Tier | Use Case |
|-----------------|---------------|----------|
| Clean | 0 | Hear original voice |
| Subtle Echo | 15 | Light effect |
| Alien | 40 | Fun formant shift |
| Fungal Speech | 60 | Word replacement hilarity |
| Musical | 85 | Voice-to-melody |
| Pure Spore | 100 | Maximum comedy |

## Trigger Methods

| Trigger | Binding | Behavior |
|---------|---------|----------|
| Manual save | F9 (default, rebindable) | Save last 30s immediately |
| Highlight save | Automatic | Save on boss kill, death, tier crossing |
| Quick save | Double-tap F9 | Save last 10s only |
| Trim mode | Hold F9 | Shows timeline UI, drag to select range |

## Highlight Auto-Save Events

| Event | Clip Duration | Pre-Event Buffer | Post-Event Buffer |
|-------|-------------|-----------------|-------------------|
| Boss killed | 15s | 10s | 5s |
| Player death | 10s | 8s | 2s |
| Infection tier 76+ crossing | 10s | 5s | 5s |
| Spore Plan execution | 8s | 3s | 5s |
| Fusion merge | 8s | 3s | 5s |

## Watermark and Metadata

All exported clips include:

| Field | Value |
|-------|-------|
| Watermark audio | 0.3s SL1C3D-L4BS logo sting at end |
| Metadata: game | "Echoes of the Spore" |
| Metadata: version | Build version string |
| Metadata: infection_tier | Tier at time of save |
| Metadata: zone | Current zone name |
| Metadata: players | Player names in session |
| Metadata: timestamp | UTC time of capture |

## Storage and Sharing

| Feature | Specification |
|---------|--------------|
| Local save path | `{persistentDataPath}/Clips/` |
| Max stored clips | 50 (oldest auto-deleted) |
| Max storage | 200 MB |
| Clipboard copy | File path copied for paste into Discord/social |
| Steam integration | Direct upload to Steam Community screenshots (audio) |
| File naming | `eots_clip_{zone}_{tier}_{timestamp}.ogg` |

## UI Integration

```
Clip Mode HUD (top-right corner):
  [REC] indicator when buffers are active (always)
  [SAVED] flash for 2s after save trigger
  
Clip Browser (pause menu → Clips):
  - List all saved clips with metadata
  - Playback with waveform visualization
  - Reprocess button (opens tier slider)
  - Delete / Export / Copy Path buttons
  - Sort by: date, zone, infection tier
```

## Performance Impact

| Operation | CPU Cost | Notes |
|-----------|----------|-------|
| Buffer recording (continuous) | 0.02ms/frame | Lock-free circular write |
| Clip save (30s) | 50ms (async) | Background thread encode |
| Reprocess (30s clip) | 200ms (async) | DSP chain on worker thread |
| Clip browser open | 20ms | Load metadata only |
| Clip playback | 0.1ms/frame | Standard audio playback |
