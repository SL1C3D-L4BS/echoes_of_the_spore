# Rust FFI Bridge Specification

## Architecture

```
Rust Workspace (rust/)
├── Cargo.toml (workspace)
└── crates/
    ├── eots-procgen-core/     # Types, RNG, validation (no_std)
    ├── eots-procgen-zone/     # Zone graph generator (no_std)
    └── eots-procgen-ffi/      # C FFI exports (std, builds .dll/.so/.dylib)

Build Output:
  → target/release/eots_procgen.dll (Windows)
  → target/release/libeots_procgen.so (Linux)
  → target/release/libeots_procgen.dylib (macOS)

Unity:
  Assets/Plugins/Native/Win64/eots_procgen.dll
  Assets/Plugins/Native/Linux/libeots_procgen.so
  Assets/Plugins/Native/macOS/libeots_procgen.dylib
```

## C FFI Interface

```rust
// eots-procgen-ffi/src/lib.rs

/// Generate a zone graph for the given zone ID and seed.
/// Returns the number of bytes written to out_buffer.
/// Returns -1 on error.
#[no_mangle]
pub extern "C" fn eots_generate_zone(
    zone_id: u8,           // 1-8
    seed: u64,             // PRNG seed
    out_buffer: *mut u8,   // Caller-allocated buffer
    buffer_capacity: u32,  // Buffer size in bytes
    out_length: *mut u32,  // Actual bytes written
) -> i32;

/// Get the version of the proc-gen library.
#[no_mangle]
pub extern "C" fn eots_version() -> u32;

/// Validate a generated zone (debug builds only).
#[no_mangle]
pub extern "C" fn eots_validate_zone(
    data: *const u8,
    length: u32,
) -> i32;  // 0 = valid, error code otherwise
```

## C# Bridge

```csharp
// ProcGenBridge.cs (EotS.ProcGen assembly)
public static class ProcGenBridge
{
    private const string LIB = "eots_procgen";

    [DllImport(LIB, CallingConvention = CallingConvention.Cdecl)]
    private static extern int eots_generate_zone(
        byte zoneId, ulong seed,
        byte[] outBuffer, uint bufferCapacity,
        out uint outLength);

    [DllImport(LIB, CallingConvention = CallingConvention.Cdecl)]
    private static extern uint eots_version();

    private static readonly byte[] _buffer = new byte[65536]; // 64KB reusable

    public static ZoneGraphData GenerateZone(int zoneId, ulong seed)
    {
        int result = eots_generate_zone(
            (byte)zoneId, seed,
            _buffer, (uint)_buffer.Length,
            out uint length);

        if (result != 0)
            throw new ProcGenException($"Zone generation failed: error {result}");

        return ZoneGraphData.Deserialize(_buffer, (int)length);
    }
}
```

## ZoneGraphData Format

```csharp
public struct ZoneGraphData
{
    public int RoomCount;
    public RoomData[] Rooms;
    public EdgeData[] Edges;  // Connections between rooms

    public struct RoomData
    {
        public int Id;
        public RoomType Type;     // Combat, Puzzle, Reward, Rest, Boss, Transition, Secret
        public int Width;         // Grid tiles (40-80)
        public int Height;        // Grid tiles (24-40)
        public int EnemyCount;    // 0-20
        public int HazardCount;   // 0-5
        public byte LootTier;     // 0-4
        public ulong RoomSeed;    // For per-room randomization
    }

    public struct EdgeData
    {
        public int FromRoom;
        public int ToRoom;
        public bool IsShortcut;   // Requires Community Tree unlock
        public bool IsSecret;     // Hidden path
    }
}
```

## PRNG: xoshiro256**

Both Rust and C# implementations produce **bit-identical** output for the same seed.

```rust
// Rust
pub struct Xoshiro256StarStar {
    s: [u64; 4],
}

impl Xoshiro256StarStar {
    pub fn new(seed: u64) -> Self;
    pub fn next_u64(&mut self) -> u64;
    pub fn next_range(&mut self, min: i32, max: i32) -> i32;
}
```

```csharp
// C# (Core/Math/Xoshiro256.cs)
public struct Xoshiro256StarStar
{
    private ulong _s0, _s1, _s2, _s3;

    public Xoshiro256StarStar(ulong seed);
    public ulong NextU64();
    public int NextRange(int min, int max);
}
```

**Verification:** Test harness generates 10,000 values from same seed in both languages, compares every value. Must be 100% identical.

## Build Integration

```bash
# Build Rust proc-gen
cd rust/
cargo build --release --target x86_64-pc-windows-msvc

# Copy to Unity
cp target/release/eots_procgen.dll ../eots/Assets/Plugins/Native/Win64/
```

## Performance Target
Zone generation: **< 100ms** for 25-room zone graph with validation.
