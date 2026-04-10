# Latency Compensation

## Overview

EotS uses a combination of input delay, client prediction, rollback, and visual
smoothing to mask network latency. The target: gameplay feels local up to 120ms RTT.

## Compensation Stack

```
Layer 1: Input Delay (2 ticks / 33ms)
  → Absorbs jitter, gives network time to deliver inputs

Layer 2: Client Prediction
  → Local player moves immediately using predicted inputs
  → Remote players move using last confirmed input (repeated)

Layer 3: Rollback + Resimulation
  → On misprediction, restore snapshot, replay with correct inputs
  → Max 10 ticks (166ms) rollback depth

Layer 4: Visual Smoothing
  → Corrections applied via interpolation, not teleport

Layer 5: Adaptive Tuning
  → Input delay and jitter buffer adjust based on measured RTT
```

## RTT Measurement

```csharp
public class LatencyTracker
{
    private CircularBuffer<float> _rttSamples;  // Last 60 samples
    private float _smoothedRTT;                  // EWMA, alpha = 0.1
    private float _jitter;                       // Std deviation of RTT
    
    public void OnPingResponse(float rttMs)
    {
        _rttSamples.Add(rttMs);
        _smoothedRTT = _smoothedRTT * 0.9f + rttMs * 0.1f;
        _jitter = ComputeStdDev(_rttSamples);
    }
}
```

| Metric | Measurement | Frequency |
|--------|------------|-----------|
| RTT | Ping/Pong packets | Every 1s |
| One-way delay (estimated) | RTT / 2 | Derived |
| Jitter | Std dev of last 60 RTT samples | Continuous |
| Packet loss | Missing sequence numbers / total | Rolling 10s window |

## Adaptive Input Delay

| RTT Range | Auto Input Delay | Rollback Budget |
|-----------|-----------------|----------------|
| 0-30ms | 1 tick (16.67ms) | 9 ticks available |
| 30-80ms | 2 ticks (33.33ms) | 8 ticks available |
| 80-130ms | 3 ticks (50.00ms) | 7 ticks available |
| 130-166ms | 3 ticks + warning | At rollback limit |
| 166ms+ | 3 ticks + degraded | Resync likely |

## Visual Correction Methods

| Entity Type | Correction Method | Duration | Rationale |
|------------|------------------|----------|-----------|
| Local player | Instant snap | 0 frames | Responsiveness priority |
| Remote players | Lerp position | 3 frames (50ms) | Smooth, no teleport |
| Projectiles | Instant snap | 0 frames | Fast-moving, lerp looks wrong |
| Enemies (AI) | Lerp position | 5 frames (83ms) | Least jarring for NPCs |
| Particles/FX | Restart at correct position | 0 frames | Visual-only, non-critical |
| Health bars | Instant | 0 frames | Gameplay-critical info |

## Hit Confirmation

Combat hit registration uses "favor the attacker" with rollback:

```
1. Attacker client detects hit locally (prediction)
2. Input sent to server with attack command
3. Server simulates with authoritative positions
4. Server confirms or rejects hit
5. If rejected: rollback attacker's damage display
6. If confirmed: apply damage on all clients
```

| Scenario | Tolerance | Behavior |
|----------|-----------|----------|
| Attacker hits, server agrees | — | Normal hit |
| Attacker hits, server disagrees (< 50ms) | Favor attacker | Grant hit |
| Attacker hits, server disagrees (50-100ms) | Context-dependent | Grant if close |
| Attacker hits, server disagrees (> 100ms) | Favor server | Reject, rollback VFX |

## Rollback Visual Budget

Maximum visual correction per rollback to keep things smooth:

| Property | Max Correction Per Frame | Notes |
|----------|------------------------|-------|
| Position (remote player) | 4 pixels | Larger corrections spread over more frames |
| Position (enemy) | 2 pixels | Even smoother for NPCs |
| Health bar | Instant | Always show true value |
| Combo counter | Instant | Always show true value |
| Animation state | Blend to correct in 2 frames | Avoid animation pops |

## Network Quality Indicators

Displayed to player in corner of HUD:

| Icon | Condition | Color |
|------|-----------|-------|
| Signal bars (4/4) | RTT < 50ms, loss < 1% | Green |
| Signal bars (3/4) | RTT 50-100ms, loss < 3% | Yellow |
| Signal bars (2/4) | RTT 100-150ms, loss < 5% | Orange |
| Signal bars (1/4) | RTT > 150ms or loss > 5% | Red |
| Disconnect icon | No packets for 3s | Red, flashing |

## Fusion Latency Considerations

Fusion merge/separate are critical state transitions:

| Action | Compensation | Notes |
|--------|-------------|-------|
| Fusion request | 2-tick input delay applies | Normal input path |
| Fusion merge confirmation | Server-authoritative only | No client prediction |
| Fused movement | Both players' inputs combined server-side | Higher misprediction rate |
| Spore Plan activation | Server-authoritative | No rollback during execution |
| Separation | Server-authoritative | Clean state split |

## Stress Test Scenarios

| Scenario | RTT Simulated | Expected Behavior |
|----------|--------------|-------------------|
| LAN perfect | 2ms | Indistinguishable from local |
| Good broadband | 40ms | No visible corrections |
| Average broadband | 80ms | Rare minor corrections |
| WiFi with jitter | 100ms + 30ms jitter | Occasional visible corrections |
| Cross-continent | 150ms | Frequent corrections, playable |
| Extreme | 200ms+ | Degraded, warning shown |
