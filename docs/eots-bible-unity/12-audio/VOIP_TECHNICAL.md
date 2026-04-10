# VOIP Technical Specification

## Codec Pipeline

```
Mic Input (OS default, 48kHz)
  -> Resample to 48kHz mono (if needed)
  -> Noise Gate (-40dB threshold, 5ms attack, 50ms release)
  -> Voice Activity Detection (VAD)
  -> Opus Encode (20ms frames, 24kbps CBR)
  -> Packet Assembly
  -> UDP Send (unreliable, sequenced)
```

## Opus Configuration

| Parameter | Value | Rationale |
|-----------|-------|-----------|
| Sample rate | 48000 Hz | Opus native rate |
| Channels | 1 (mono) | No stereo for voice |
| Bitrate | 24000 bps | Good quality, low bandwidth |
| Frame size | 20ms (960 samples) | Standard VoIP frame |
| Application mode | OPUS_APPLICATION_VOIP | Optimized for speech |
| Complexity | 5 (of 10) | Balance CPU vs quality |
| DTX | Enabled | Discontinuous transmission during silence |
| FEC | Enabled | Forward error correction |
| Packet loss % hint | 5 | Tune FEC for expected loss |
| Max payload | 256 bytes | Fits in single UDP packet |

## Voice Activity Detection (VAD)

| Parameter | Value |
|-----------|-------|
| Algorithm | Energy-based + Opus built-in |
| Threshold | -35 dB RMS |
| Hold time | 300ms after last speech |
| Min speech duration | 50ms (debounce) |
| Packets during silence | None (DTX handles) |

## Jitter Buffer

| Parameter | Value |
|-----------|-------|
| Type | Adaptive |
| Min depth | 2 frames (40ms) |
| Default depth | 3 frames (60ms) |
| Max depth | 8 frames (160ms) |
| Adaptation rate | +1 frame per 3 late packets, -1 frame per 5s stable |
| PLC method | Opus native PLC |
| Max consecutive PLC | 5 frames (100ms), then mute |

## Packet Format

```
VoicePacket (max 270 bytes):
  [Header: 4B]
    type:       4 bits  (VOICE = 3)
    flags:      4 bits  (0x0)
    sequence:   24 bits (wrapping counter)
  [Voice Header: 6B]
    player_id:  8 bits
    timestamp:  32 bits (sample count, wraps at 2^32)
    frame_count: 8 bits (frames in this packet, 1-3)
  [Opus Frame(s): variable]
    frame_len:  16 bits per frame
    frame_data: variable per frame
```

## Threading Model

```
Audio Thread (high priority, real-time)
  -> Mic capture callback (OS audio driver)
  -> Noise gate + VAD
  -> Opus encode
  -> Push to send queue (lock-free ring buffer)

Network Thread
  -> Dequeue encoded packets
  -> Assemble VoicePacket
  -> UDP send to all peers (or server relay)
  -> Receive inbound voice packets
  -> Push to per-player jitter buffer

Audio Render Thread (FMOD mixer callback)
  -> For each remote player:
    -> Pull from jitter buffer (or PLC)
    -> Opus decode
    -> Infection DSP chain (see SPORE_TALK_DSP.md)
    -> Proximity attenuation + panning
    -> Mix to Voice_VOIP bus
```

## Proximity System

```csharp
float ComputeVoiceVolume(float distance, bool isFused)
{
    if (isFused) return 1.0f;  // Fused partners always full volume
    if (distance <= 5f) return 1.0f;
    if (distance >= 20f) return 0.0f;
    return 1.0f - ((distance - 5f) / 15f);
}

float ComputeVoicePan(Vector2 listenerPos, Vector2 speakerPos)
{
    float dx = speakerPos.x - listenerPos.x;
    return Mathf.Clamp(dx / 15f, -1f, 1f);  // -1 left, +1 right
}
```

## Bandwidth Per Player

| Scenario | Upload | Download (3 peers) |
|----------|--------|-------------------|
| All talking | 3.0 KB/s | 9.0 KB/s |
| 1 talking (DTX) | 0.1 KB/s | 3.1 KB/s |
| All silent (DTX) | 0.1 KB/s | 0.3 KB/s |

## Platform-Specific Notes

| Platform | Mic Access | Permissions | Notes |
|----------|-----------|-------------|-------|
| PC (Windows) | WASAPI | OS mic permission | Default device auto-select |
| PC (Linux) | PulseAudio | None | Steamworks audio routing |
| PS5 | Sony Audio SDK | System mic setting | DualSense mic supported |
| Xbox | GameChat 2 | Xbox privacy settings | Platform voice policy |
| Switch 2 | Nintendo Audio | Parental controls | Headset mic only |

## Error Handling

| Condition | Response |
|-----------|----------|
| Mic disconnected | Mute local, notify UI, poll for reconnect every 2s |
| Opus encode failure | Skip frame, log warning |
| Opus decode failure | Trigger PLC for that frame |
| Jitter buffer underrun | PLC, increase buffer depth |
| Jitter buffer overrun | Drop oldest frame |
| Network timeout (5s no packets) | Show "connection lost" icon on player |
