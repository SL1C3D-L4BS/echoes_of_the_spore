# Replay System

## Overview

The replay system records all inputs and initial state for deterministic playback.
Because the simulation is deterministic, replaying inputs reproduces exact gameplay.

## Recording Format

```
Replay File (.eotsr):
  [Header: 64B]
    magic:          4B  "EOTS"
    version:        4B  Replay format version
    build_version:  16B Build hash
    seed:           8B  xoshiro256** initial seed
    zone_id:        4B  Zone number
    heat_level:     4B  Heat setting
    player_count:   4B  1-4
    player_classes: 4B  1 byte per player (class enum)
    total_ticks:    4B  Total simulation ticks recorded
    duration_ms:    4B  Real-time duration
    checksum:       4B  xxHash32 of all input data
    reserved:       4B  Padding
  [Initial State: var]
    compressed_state: Full world snapshot at tick 0 (zstd compressed)
  [Input Stream: var]
    For each tick:
      [tick: 4B]
      [input_mask: 1B]  Bitmask of which players have input this tick
      [InputCommand × N: 12B each]  Only present for bits set in mask
  [Metadata: var]
    JSON blob with session stats, player names, timestamps
```

## File Size Estimates

| Session Length | Input Data | Compressed Total |
|---------------|-----------|-----------------|
| 10 min (36000 ticks) | ~1.7 MB | ~400 KB |
| 30 min (108000 ticks) | ~5.2 MB | ~1.2 MB |
| 60 min (216000 ticks) | ~10.4 MB | ~2.4 MB |

## Playback Controls

| Control | Binding | Action |
|---------|---------|--------|
| Play/Pause | Space | Toggle playback |
| Speed 0.25x | 1 | Quarter speed |
| Speed 0.5x | 2 | Half speed |
| Speed 1x | 3 | Normal speed |
| Speed 2x | 4 | Double speed |
| Speed 4x | 5 | Quad speed |
| Step forward | Right arrow | Advance 1 tick |
| Step back | Left arrow | Rewind 1 tick (restore snapshot) |
| Jump to tick | Ctrl+G | Enter tick number |
| Jump to start | Home | Tick 0 |
| Jump to end | End | Last tick |
| Toggle camera | Tab | Cycle followed player |
| Free camera | F | Detached camera mode |

## Snapshot Checkpoints

To enable fast seeking, snapshots are stored periodically during recording:

| Checkpoint interval | 600 ticks (10 seconds) |
|--------------------|-|
| Snapshot size | ~30 KB each |
| Snapshots per 30-min session | 180 |
| Total checkpoint data | ~5.4 MB (compressed ~1.3 MB) |

Seeking to any tick: restore nearest prior checkpoint, resimulate forward.
Max resimulation on seek: 599 ticks (~10ms at optimized speed).

## Replay Viewer (Editor)

| Feature | Description |
|---------|-------------|
| Location | `Editor/ReplayViewer.cs` |
| Timeline | Scrubable timeline with tick markers |
| Entity inspector | Click entity to view component state at current tick |
| Input display | Show all player inputs per tick |
| Desync detection | Compare checksums against reference |
| Event markers | Boss kills, deaths, fusion events marked on timeline |
| Export frame | Screenshot current frame |
| Diff view | Compare two replays side-by-side (same seed) |

## Determinism Verification

```csharp
public bool VerifyReplay(ReplayFile replay)
{
    var world = new EotSWorld(replay.Seed);
    world.LoadState(replay.InitialState);
    
    for (int tick = 0; tick < replay.TotalTicks; tick++)
    {
        var inputs = replay.GetInputsForTick(tick);
        world.SetInputs(inputs);
        world.Tick();
        
        if (tick % 600 == 0)  // Every checkpoint
        {
            var hash = world.ComputeChecksum();
            if (hash != replay.GetCheckpointHash(tick))
                return false;  // DESYNC: replay diverges
        }
    }
    
    return world.ComputeChecksum() == replay.FinalChecksum;
}
```

## Use Cases

| Use Case | Description |
|----------|-------------|
| Bug reproduction | Load replay, seek to bug tick, inspect state |
| Balance analysis | Replay boss fights, measure DPS/time |
| Desync diagnosis | Compare server/client replays for divergence |
| Content creation | Record runs for trailers/marketing |
| Speedrun verification | Validate speedrun legitimacy |
| QA regression | Re-run previous sessions after code changes |
| Player sharing | Share `.eotsr` files for community highlights |

## Storage

| Setting | Value |
|---------|-------|
| Auto-save replays | Last 5 sessions |
| Max replay storage | 100 MB |
| Save location | `{persistentDataPath}/Replays/` |
| File naming | `eots_{zone}_{heat}_{timestamp}.eotsr` |
| Cloud backup | Optional Steam Cloud sync |

## Limitations

| Limitation | Reason | Mitigation |
|-----------|--------|------------|
| No visual replay (audio/VFX not captured) | Deterministic sim only | Re-render from state |
| Build version must match | Different builds may diverge | Version check in header |
| No VOIP in replay | Voice not recorded | Clip Mode handles voice |
| Large file for long sessions | Input data accumulates | zstd compression |
| Rewind requires resimulation | No reverse simulation | Checkpoint snapshots |
