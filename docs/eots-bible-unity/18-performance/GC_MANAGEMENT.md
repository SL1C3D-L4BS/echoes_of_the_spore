# GC Management

## Strategy

EotS uses Unity's Incremental GC with aggressive allocation avoidance in hot paths.
The custom ECS simulation (Core/) produces zero managed heap allocations during
gameplay. Bridge and UI layers minimize allocations.

## GC Configuration

| Setting | Value | Where |
|---------|-------|-------|
| Incremental GC | Enabled | Player Settings |
| GC time slice | 1ms per frame max | Scripting runtime config |
| Manual GC.Collect | Only on loading screens | Code |
| GC mode during gameplay | Incremental only | Code |
| GC mode during loading | Full collection allowed | Code |

## Allocation Budget by Layer

| Layer | Budget (bytes/frame) | Enforcement |
|-------|---------------------|-------------|
| Core/ (ECS, systems, math) | 0 | Roslyn analyzer, CI gate |
| Netcode/ | 0 | Roslyn analyzer, CI gate |
| Bridge/ | < 256 bytes | Weekly profiling review |
| UI/ | < 1024 bytes | Weekly profiling review |
| Audio/ | < 128 bytes | FMOD uses native memory |
| Editor/ | Unlimited | Not in builds |

## Zero-Allocation Enforcement (Core/)

### Roslyn Analyzer Rules

```csharp
// Custom Roslyn analyzer: EotS.Analyzers
[DiagnosticAnalyzer(LanguageNames.CSharp)]
public class NoHeapAllocationAnalyzer : DiagnosticAnalyzer
{
    // EOTS001: new reference type in Core/
    // EOTS002: LINQ usage in Core/
    // EOTS003: String concatenation in Core/
    // EOTS004: Boxing (value type to object) in Core/
    // EOTS005: Lambda/delegate allocation in Core/
    // EOTS006: params array allocation in Core/
    // EOTS007: yield return (generates state machine) in Core/
    // EOTS008: async/await (generates state machine) in Core/
}
```

| Analyzer ID | Violation | Severity |
|------------|-----------|----------|
| EOTS001 | `new List<T>()`, `new Dictionary<>()` in hot path | Error |
| EOTS002 | `.Where()`, `.Select()`, `.ToList()` | Error |
| EOTS003 | `"str" + var` (use Span or StringBuilder) | Error |
| EOTS004 | Value type passed as `object` | Error |
| EOTS005 | `() => expr` capturing local | Error |
| EOTS006 | `params T[]` in hot path | Warning |
| EOTS007 | `yield return` in simulation code | Error |
| EOTS008 | `async`/`await` in simulation code | Error |

## Common Allocation Sources and Fixes

| Source | Allocation | Fix |
|--------|-----------|-----|
| `List<T>.Add()` when resizing | Array copy | Pre-allocate capacity |
| `Dictionary<K,V>` resize | Bucket array | Pre-allocate, use arrays |
| `string.Format()` | String heap alloc | Use Span<char> or cached |
| `foreach` on non-struct enumerator | Enumerator object | Use `for` loop with index |
| `Debug.Log()` | String alloc | Strip from release builds |
| `GetComponent<T>()` | Managed wrapper | Cache in Start(), use direct ref |
| `Physics2D.OverlapCircle()` | Allocator array | Use NonAlloc variants |
| `Delegate.Combine()` | Delegate chain | Direct method call |
| LINQ `.Any()`, `.Count()` | Enumerator | Manual loop |
| Boxing in `Equals()` | `object` cast | Implement `IEquatable<T>` |

## Pre-Allocation Inventory

| System | Pre-Allocated At | Size | Lifetime |
|--------|-----------------|------|----------|
| Entity array | World creation | 4096 × sizeof(Entity) | World lifetime |
| Component arrays (per type) | World creation | 4096 × sizeof(T) | World lifetime |
| Snapshot ring buffer | World creation | 120 × 30 KB | World lifetime |
| Input buffers (x4) | Session start | 64 × 12 B each | Session |
| Collision pair buffer | World creation | 1024 × 8 B | World lifetime |
| Event queue | World creation | 256 × 32 B | World lifetime |
| Network send buffer | Session start | 64 KB | Session |
| Network recv buffer | Session start | 64 KB | Session |
| String builder (UI) | UI init | 1 KB | Scene lifetime |
| Object pools (enemies) | Zone load | 50 per type | Zone lifetime |
| Object pools (projectiles) | Zone load | 100 | Zone lifetime |
| Object pools (VFX) | Zone load | 50 | Zone lifetime |

## GC Timing Strategy

```csharp
public class GCController : MonoBehaviour
{
    public void OnLoadingScreenStart()
    {
        // Safe to do full GC during loading
        GC.Collect();
        GC.WaitForPendingFinalizers();
        GC.Collect();
    }
    
    public void OnGameplayStart()
    {
        // Switch to incremental only
        GarbageCollector.GCMode = GarbageCollector.Mode.Enabled;
        // Incremental GC will run in small time slices
    }
    
    // NEVER call GC.Collect() during gameplay
}
```

## Monitoring GC in Builds

| Metric | How to Measure | Alert |
|--------|---------------|-------|
| GC alloc per frame | Unity Profiler "GC Alloc" | > 0 in Core/ |
| GC collect frequency | Profiler timeline | > 1/min during gameplay |
| GC pause duration | Profiler (incremental slices) | > 2ms single slice |
| Total managed heap | Profiler memory module | > 100 MB |
| Fragmentation | Memory Profiler snapshot | > 30% fragmented |

## GC Red Lines

| Metric | Red Line | Action |
|--------|----------|--------|
| Any allocation in Core/ during gameplay | P0 | Fix immediately |
| GC pause > 3ms | P1 | Investigate and reduce |
| GC collect during boss fight | P1 | Ensure pre-allocated |
| Managed heap growth > 5 MB/min | P1 | Memory leak investigation |
| Total managed heap > 150 MB | P2 | Optimize allocations |

## IL2CPP Considerations

| Behavior | Impact |
|----------|--------|
| Value types always stack-allocated | Good: no GC for structs |
| Generics generate specialized code | Good: no boxing |
| Array bounds checks | Small CPU cost, can disable in Release |
| Null checks | Small CPU cost, can disable in Release |
| `Span<T>` and `stackalloc` | Supported in .NET Standard 2.1 |
| `ref struct` | Enforced stack-only, good for temp data |
