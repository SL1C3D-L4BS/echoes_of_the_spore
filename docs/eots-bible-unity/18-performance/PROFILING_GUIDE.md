# Profiling Guide

## When to Profile

| Trigger | Action |
|---------|--------|
| Frame time > 20ms for 3+ frames | P0: Profile immediately |
| ECS tick > 5ms | P0: Profile ECS systems |
| Memory > 80% of budget | Profile memory allocations |
| Load time regression | Profile scene load pipeline |
| Network rollback spike | Profile rollback resimulation |
| Pre-milestone | Full profiling pass on all platforms |
| Weekly | 30-minute profiling session |

## Profiling Workflow

### Step 1: Identify the Problem

```
1. Open Unity Profiler (Window > Analysis > Profiler)
2. Connect to target (local editor or remote build)
3. Play the game, reproduce the issue
4. Look at CPU Usage module:
   - Find the spike frame
   - Identify the top-level contributor (ECS, Rendering, Audio, etc.)
```

### Step 2: Drill Down

| If Top Contributor Is | Next Step |
|----------------------|-----------|
| ECS Tick | Check custom ECS profiler (F1 overlay) for per-system times |
| Rendering | Open Frame Debugger (Window > Analysis > Frame Debugger) |
| Audio | Open FMOD Profiler (FMOD > Profiler) |
| Network | Check network profiler (F2 overlay) |
| GC Collect | Enable Deep Profile, find allocation source |
| Physics (should be none) | Investigate unexpected physics usage |
| UI | Check Canvas rebuild frequency |

### Step 3: Measure Baseline

Before optimizing, record baseline numbers:

| Metric | How to Measure | Record |
|--------|---------------|--------|
| Frame time P50/P95/P99 | Unity Profiler, 60s sample | Screenshot + values |
| ECS tick time | Custom profiler, 60s sample | Per-system breakdown |
| Memory usage | Memory Profiler snapshot | Total + per-category |
| Draw calls | Frame Debugger | Count + batching ratio |
| GC allocations | Deep Profile, 60s | Bytes per frame |

### Step 4: Optimize and Validate

```
1. Make ONE change at a time
2. Re-profile with same scenario
3. Compare against baseline
4. If improved: commit with perf annotation in message
5. If no improvement or regression: revert
```

## Platform-Specific Profiling

### PC (Editor)

| Tool | Access | Best For |
|------|--------|----------|
| Unity Profiler | Built-in | Frame-by-frame CPU/GPU |
| Deep Profile | Profiler toggle | Per-method allocations |
| Memory Profiler | Package Manager | Heap snapshots |
| Frame Debugger | Built-in | Draw call analysis |
| RenderDoc | External | GPU shader debugging |

### PC (IL2CPP Build)

| Tool | Access | Best For |
|------|--------|----------|
| Unity Profiler (remote) | Autoconnect or IP | Production-like profiling |
| Superluminal | Attach to process | Native code hot paths |
| Tracy | Instrument code | Detailed timeline view |
| Windows Task Manager | Built-in | Quick memory/CPU check |

### PS5

| Tool | Access | Best For |
|------|--------|----------|
| Razor CPU | PS5 dev kit | CPU sampling + instrumentation |
| Razor GPU | PS5 dev kit | GPU workload analysis |
| Unity Profiler | Network connect to dev kit | Managed code profiling |
| Memory Analyzer | Sony tools | Console memory layout |

### Xbox Series

| Tool | Access | Best For |
|------|--------|----------|
| PIX | Xbox dev kit | CPU + GPU capture |
| Unity Profiler | Network connect | Managed code |
| Xbox Performance Toolkit | GDK tools | System-level analysis |

### Switch 2

| Tool | Access | Best For |
|------|--------|----------|
| NVN Performance | Nintendo SDK | GPU analysis |
| Unity Profiler | USB connect | Managed code |
| NX CPU Profiler | Nintendo SDK | ARM CPU analysis |

## Common Performance Issues and Solutions

| Issue | Symptom | Solution |
|-------|---------|----------|
| GC spike | 5-10ms frame spike every few seconds | Pool objects, avoid allocations in hot paths |
| Draw call explosion | GPU-bound, high batch count | Atlas sprites, reduce materials, SRP batcher |
| ECS tick spike | ECS tick > 4ms | Reduce entity count, optimize spatial hash |
| Snapshot too large | Serialize > 500us | Compress, delta-encode, reduce component size |
| Tilemap overdraw | GPU-bound in rooms | Cull off-screen tiles, reduce layers |
| FMOD voice saturation | Audio pops/drops | Reduce max voices, prioritize stealing |
| UI Canvas rebuild | Spike on UI change | Separate dynamic/static canvases |

## Profiling Checklist (Pre-Milestone)

| Check | Tool | Budget | Actual | Pass? |
|-------|------|--------|--------|-------|
| Frame time P95 | Unity Profiler | < 16.67ms | __ ms | |
| ECS tick P95 | Custom profiler | < 4.0ms | __ ms | |
| Snapshot serialize | Custom profiler | < 500us | __ us | |
| Memory total | Memory Profiler | < 500 MB | __ MB | |
| GC alloc per frame | Deep Profile | < 1 KB | __ KB | |
| Draw calls | Frame Debugger | < 200 | __ | |
| Load time (zone) | Stopwatch | < 3s | __ s | |
| Audio CPU | FMOD Profiler | < 5% | __ % | |
| Network bandwidth | Custom profiler | < 23 KB/s | __ KB/s | |

## Performance Annotations

Use Unity ProfilerMarker for custom instrumentation:

```csharp
private static readonly ProfilerMarker s_MovementMarker = 
    new ProfilerMarker("EotS.MovementSystem");

public static void Tick(EotSWorld world)
{
    using (s_MovementMarker.Auto())
    {
        // System logic here
    }
}
```

| Where to Instrument | Why |
|---------------------|-----|
| Each ECS system | Per-system time breakdown |
| Snapshot serialize/deserialize | Serialization cost |
| Rollback resimulation loop | Rollback overhead |
| Network packet processing | I/O cost |
| FMOD event creation | Audio overhead |
| Room instantiation | Loading cost |
