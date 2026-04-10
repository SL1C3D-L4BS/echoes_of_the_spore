# Input Buffer

## Overview

Each player (local and remote) has a per-player input buffer that stores recent
InputCommands indexed by tick. The buffer enables rollback, input prediction, and
redundant transmission for packet loss resilience.

## Buffer Structure

```csharp
public class InputBuffer
{
    private InputCommand[] _buffer;    // Ring buffer
    private int _capacity;             // 64 ticks (just over 1 second)
    private int _lastReceivedTick;     // Most recent confirmed input tick
    private int _lastPredictedTick;    // Most recent predicted tick
    
    public InputCommand Get(int tick) => _buffer[tick % _capacity];
    public void Set(int tick, InputCommand cmd) => _buffer[tick % _capacity] = cmd;
}
```

## Configuration

| Parameter | Value | Notes |
|-----------|-------|-------|
| Buffer capacity | 64 ticks | ~1.07s at 60Hz |
| Input delay | 2 ticks (33ms) | Configurable 1-3 via settings |
| Redundant send count | 3 | Each packet carries 3 most recent ticks |
| Stale threshold | 30 ticks (500ms) | Drop inputs older than this |
| Prediction method | Repeat last confirmed | Simple, effective for action games |

## InputCommand Structure

```csharp
public struct InputCommand  // 12 bytes total
{
    public byte MoveX;          // -1, 0, +1 encoded as 0, 1, 2
    public byte MoveY;          // -1, 0, +1 encoded as 0, 1, 2
    public ushort ButtonFlags;  // Bitfield (16 buttons max)
    public int Tick;            // Simulation tick this input applies to
    public int Checksum;        // xxHash32 of above fields
}
```

## Button Flag Bits

| Bit | Button | Action |
|-----|--------|--------|
| 0 | Jump | Jump / wall jump |
| 1 | Attack Light | Light attack |
| 2 | Attack Heavy | Heavy attack |
| 3 | Dash | Dash |
| 4 | Interact | NPC / door / pickup |
| 5 | Fusion Request | Request fusion merge |
| 6 | Fusion Separate | Request separation |
| 7 | Spore Plan | Activate spore plan |
| 8 | Heal | Use heal ability |
| 9 | Block | Shield / block |
| 10 | Map Toggle | Open/close map |
| 11-15 | Reserved | Future use |

## Input Delay Pipeline

```
Frame N:   Player presses Jump
Frame N:   InputCommand created with tick = current_tick + input_delay
Frame N:   Command stored in local buffer at tick (current_tick + 2)
Frame N:   Command sent to server (with redundant ticks N, N-1, N-2)
Frame N+2: Command applied to simulation
```

| Input Delay Setting | Latency Added | Best For |
|-------------------|--------------|----------|
| 1 tick (16.67ms) | Minimal | LAN play, < 30ms RTT |
| 2 ticks (33.33ms) | Default | Online play, 30-100ms RTT |
| 3 ticks (50.00ms) | Maximum | High latency, 100-150ms RTT |

## Redundant Transmission

Each InputMsg packet contains the last 3 ticks of input:

```
InputMsg Packet:
  [Header: 4B]
  [player_id: 1B]
  [tick_count: 1B]           // Always 3 (or fewer at game start)
  [InputCommand[0]: 12B]     // Current tick
  [InputCommand[1]: 12B]     // Previous tick
  [InputCommand[2]: 12B]     // Two ticks ago
  Total: ~42 bytes
```

This means a single dropped packet causes no prediction error if the next packet arrives.

## Input Prediction for Remote Players

| Scenario | Prediction Strategy |
|----------|-------------------|
| No input received yet | Neutral (no buttons, no movement) |
| 1 tick missing | Repeat last confirmed input |
| 2-3 ticks missing | Repeat last confirmed input |
| 4-10 ticks missing | Repeat last, flag as degraded |
| 10+ ticks missing | Trigger state resync from server |

## Prediction Accuracy Tracking

```csharp
public struct PredictionStats
{
    public int TotalPredictions;
    public int CorrectPredictions;
    public int MispredictionCount;
    public float AccuracyPercent => (float)CorrectPredictions / TotalPredictions * 100;
}
```

| Metric | Target | Alert Threshold |
|--------|--------|----------------|
| Prediction accuracy | > 95% | < 85% triggers input delay increase |
| Mispredictions per second | < 3 | > 5 triggers rollback warning |
| Max consecutive mispredictions | < 3 | > 5 triggers resync |

## Server-Side Input Buffer

The server maintains per-player input buffers with additional validation:

| Check | Action on Failure |
|-------|------------------|
| Tick is within valid window | Drop packet, log warning |
| Checksum matches payload | Drop packet, log warning |
| Button flags are valid | Mask to valid bits only |
| Input rate is reasonable | Rate limit to 65/s (slight tolerance) |
| Player is alive | Accept but mark as no-op |

## Frame Advantage / Disadvantage

```
Frame advantage = local_tick - remote_tick

If frame_advantage > 3:
  -> Local player is "ahead" — add 1 tick input delay temporarily
  -> Prevents one player from having consistent prediction advantage

If frame_advantage < -3:
  -> Local player is "behind" — reduce input delay by 1 (minimum 1)
  -> Helps catch up to remote player
```

## Debug Visualization

In development builds, F3 toggles input buffer overlay:

| Display | Content |
|---------|---------|
| Ring buffer visual | Color-coded: green=confirmed, yellow=predicted, red=missing |
| Prediction accuracy | Rolling 60s accuracy percentage |
| Frame advantage | Current value + graph |
| Input delay | Current setting (auto-adjusted or manual) |
