# 15 — PROCEDURAL GENERATION & CONTENT VALIDATION

## Architecture: Rust → C FFI → Unity [DllImport]

### Integration Path
```
Rust (eots-procgen-ffi) compiles to:
  libeots_procgen.so (Linux) / eots_procgen.dll (Windows) / libeots_procgen.dylib (macOS)
    ↓
Placed in Assets/Plugins/Native/{platform}/
    ↓
C# ProcGenBridge.cs uses [DllImport("eots_procgen")] to call Rust FFI functions
    ↓
Returns binary buffer → parsed into ZoneGraphData C# struct
    ↓
RoomSpawner.cs instantiates Unity Tilemap prefabs based on graph
```

### C# FFI Wrapper
```csharp
// Assets/_Project/Scripts/ProcGen/ProcGenBridge.cs
using System.Runtime.InteropServices;

public static class ProcGenBridge
{
    [DllImport("eots_procgen")]
    private static extern int eots_procgen_generate(
        byte zoneId, ulong seed,
        byte[] outBuffer, uint bufferCapacity,
        out uint outLength);

    public static ZoneGraphData GenerateZone(byte zoneId, ulong seed) { ... }
}
```

### Room Instantiation in Unity
1. ProcGenBridge.GenerateZone() returns ZoneGraphData (room nodes + edges)
2. For each room: lookup prefab from Resources or Addressables by zone + type
3. Each prefab contains: Tilemap (collision), child GameObjects with Spawner scripts
4. RoomSpawner instantiates prefab, creates ECS entities for enemies, connects rooms
5. Rooms > 2 away from player: SetActive(false) (Unity standard culling)

## Generation Algorithm
Same as all previous bible versions — 5 phases: critical path, branching, loops, secrets, validation. See complete spec in this file's prior versions. Algorithm is engine-independent (runs in Rust).

## Room Type Weights
| Type | Sporehaven | Cortex | Ferment | Chitin | Rot | Forge | Necroveil | Core |
|---|---|---|---|---|---|---|---|---|
| Combat | 30 | 25 | 20 | 40 | 25 | 25 | 30 | 35 |
| Puzzle | 10 | 20 | 25 | 5 | 15 | 25 | 15 | 10 |
| Reward | 20 | 15 | 15 | 15 | 15 | 10 | 10 | 10 |
| Rest | 15 | 10 | 10 | 10 | 15 | 10 | 5 | 5 |
| Boss | 5 | 5 | 5 | 10 | 5 | 5 | 10 | 20 |
| Transition | 10 | 15 | 15 | 10 | 15 | 15 | 15 | 10 |
| Secret | 10 | 10 | 10 | 10 | 10 | 10 | 15 | 10 |

## Quality Target
1000 zones per config, all pass validation, retry rate < 5%.
