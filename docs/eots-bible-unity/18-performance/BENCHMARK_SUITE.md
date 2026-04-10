# Benchmark Suite

## Overview

Automated benchmarks run nightly to detect performance regressions. Results are tracked
over time and compared against baselines. Any regression > 10% triggers investigation.

## Benchmark Categories

### ECS Benchmarks

| Benchmark | Setup | Metric | Baseline Target |
|-----------|-------|--------|----------------|
| ECS_Tick_Empty | 0 entities, 1 tick | Time (us) | < 10 us |
| ECS_Tick_100 | 100 entities, all systems | Time (us) | < 200 us |
| ECS_Tick_500 | 500 entities, all systems | Time (us) | < 1000 us |
| ECS_Tick_1000 | 1000 entities, all systems | Time (us) | < 2500 us |
| ECS_Tick_4096 | 4096 entities (max), all systems | Time (us) | < 5000 us |
| ECS_CreateDestroy | Create + destroy 1000 entities | Time (us) | < 500 us |
| ECS_Query_All | Query all entities with Position | Time (us) | < 50 us |
| ECS_Query_Filtered | Query entities with 3 components | Time (us) | < 100 us |

### Math Benchmarks

| Benchmark | Operations | Metric | Baseline Target |
|-----------|-----------|--------|----------------|
| FP64_Add | 1M additions | Time (ms) | < 5 ms |
| FP64_Multiply | 1M multiplications | Time (ms) | < 8 ms |
| FP64_Divide | 1M divisions | Time (ms) | < 15 ms |
| FP64_Sqrt | 1M square roots | Time (ms) | < 20 ms |
| Vec2_Normalize | 1M normalizations | Time (ms) | < 25 ms |
| Vec2_Distance | 1M distance calculations | Time (ms) | < 10 ms |

### Serialization Benchmarks

| Benchmark | Setup | Metric | Baseline Target |
|-----------|-------|--------|----------------|
| Snapshot_Serialize_100 | 100 entities | Time (us) + Size (bytes) | < 100 us, < 5 KB |
| Snapshot_Serialize_500 | 500 entities | Time (us) + Size (bytes) | < 300 us, < 25 KB |
| Snapshot_Deserialize_100 | 100 entities | Time (us) | < 80 us |
| Snapshot_Deserialize_500 | 500 entities | Time (us) | < 250 us |
| Checksum_100 | 100 entities | Time (us) | < 30 us |
| Checksum_500 | 500 entities | Time (us) | < 100 us |

### Collision Benchmarks

| Benchmark | Setup | Metric | Baseline Target |
|-----------|-------|--------|----------------|
| SpatialHash_Insert_1000 | 1000 entities | Time (us) | < 200 us |
| SpatialHash_Query_100 | 100 queries, 1000 entities | Time (us) | < 500 us |
| NarrowPhase_100_Pairs | 100 AABB overlaps | Time (us) | < 50 us |
| FullCollision_500 | 500 entities, broad + narrow | Time (us) | < 800 us |

### Network Benchmarks

| Benchmark | Setup | Metric | Baseline Target |
|-----------|-------|--------|----------------|
| InputBuffer_ReadWrite | 10K read/write cycles | Time (us) | < 100 us |
| Rollback_Resim_1 | 1-tick rollback, 500 entities | Time (us) | < 2500 us |
| Rollback_Resim_5 | 5-tick rollback, 500 entities | Time (us) | < 12500 us |
| Rollback_Resim_10 | 10-tick rollback, 500 entities | Time (us) | < 25000 us |
| StateMsg_Compress | 500-entity state, zstd | Time (us) + Ratio | < 200 us, > 3:1 |

### Rendering Benchmarks (PlayMode)

| Benchmark | Setup | Metric | Baseline Target |
|-----------|-------|--------|----------------|
| Render_EmptyRoom | 1 empty room | Frame time (ms) | < 4 ms |
| Render_FullRoom | Room + 20 enemies + VFX | Frame time (ms) | < 8 ms |
| Render_BossRoom | Boss + 4 players + full VFX | Frame time (ms) | < 10 ms |
| DrawCall_Count | Full gameplay scene | Draw calls | < 100 |

## Benchmark Runner

```csharp
[TestFixture]
[Category("Benchmark")]
public class ECSBenchmarks
{
    [Test]
    [Performance]
    public void ECS_Tick_500_Entities()
    {
        var world = BenchmarkWorldFactory.Create(entityCount: 500);
        var inputs = BenchmarkInputFactory.Create(playerCount: 4);
        
        Measure.Method(() =>
        {
            world.SetInputs(inputs);
            world.Tick();
        })
        .WarmupCount(10)
        .MeasurementCount(100)
        .Run();
    }
}
```

## CI Integration

```yaml
# .github/workflows/benchmarks.yml
name: Nightly Benchmarks
on:
  schedule:
    - cron: '0 3 * * *'  # 3 AM UTC daily
  workflow_dispatch:

jobs:
  benchmark:
    runs-on: ubuntu-latest  # Consistent hardware
    steps:
      - uses: actions/checkout@v4
      - name: Run benchmarks
        run: dotnet test --filter "Category=Benchmark" --logger "json"
      - name: Compare against baseline
        run: python scripts/benchmark_compare.py results.json baseline.json
      - name: Upload results
        uses: actions/upload-artifact@v4
        with: { name: benchmarks, path: results.json }
      - name: Alert on regression
        if: failure()
        run: python scripts/alert_regression.py
```

## Regression Detection

| Metric | Regression Threshold | Action |
|--------|---------------------|--------|
| Any benchmark > 10% slower | Warning | Investigate, create ticket |
| Any benchmark > 25% slower | Alert | P1 bug, block release |
| Any benchmark > 50% slower | Critical | P0 bug, immediate fix |
| Memory benchmark > 15% higher | Warning | Investigate allocation |
| Serialization size > 10% larger | Warning | Review component changes |

## Baseline Management

| Action | When |
|--------|------|
| Establish baseline | Start of each milestone |
| Update baseline | After intentional architectural changes |
| Compare daily | Nightly CI run |
| Archive baselines | Keep last 30 days |
| Hardware consistency | Same CI runner type for all runs |

## Results Dashboard

Grafana dashboard showing benchmark trends:

| Panel | Content |
|-------|---------|
| ECS tick time (7-day trend) | Line chart per entity count |
| Math operations (7-day trend) | Line chart per operation |
| Serialization (7-day trend) | Time + size dual axis |
| Collision (7-day trend) | Line chart per entity count |
| Rollback cost (7-day trend) | Line chart per depth |
| Regression alerts | Table of recent violations |
| Baseline comparison | Current vs baseline delta % |

## Platform Benchmark Comparison

Run monthly on all target platforms:

| Benchmark | PC (i7-10700) | PS5 | Xbox X | Xbox S | Switch 2 |
|-----------|-------------|-----|--------|--------|----------|
| ECS_Tick_500 | 1.0 ms | 1.2 ms | 1.2 ms | 1.5 ms | 2.5 ms |
| FP64_Multiply_1M | 8 ms | 10 ms | 10 ms | 12 ms | 20 ms |
| Snapshot_Ser_500 | 300 us | 350 us | 350 us | 400 us | 600 us |
| Rollback_10 | 25 ms | 30 ms | 30 ms | 35 ms | 50 ms |

Values are targets; actual measurements filled in during profiling sessions.
