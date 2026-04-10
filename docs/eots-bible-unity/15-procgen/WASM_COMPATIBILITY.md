# WASM Compatibility

## Overview

The Rust procgen crate (`eots-procgen-core` and `eots-procgen-zone`) is designed to compile to WebAssembly (WASM) for future web and mobile spectator clients. This document specifies the `no_std` requirements, WASM build configuration, memory constraints, and integration path.

## Target Use Cases

| Use Case | Platform | Priority | Status |
|----------|----------|----------|--------|
| Web spectator client | Browser (WASM) | Post-launch | Planned |
| Mobile companion app | iOS/Android (via WASM runtime) | Post-launch | Planned |
| Server-side validation | Node.js (WASM module) | Pre-launch | Active |
| Automated testing | CI pipeline (WASM) | Pre-launch | Active |

## no_std Architecture

### Crate Dependency Map

```
eots-procgen-core (no_std, no alloc)
  - Types, enums, constants
  - Xoshiro256** PRNG
  - Seed derivation
  - Integer math utilities

eots-procgen-zone (no_std, alloc required)
  - Zone graph generator (5 phases)
  - Validation
  - Serialization
  - Uses alloc::vec::Vec for room/edge lists

eots-procgen-ffi (std, NOT WASM-compatible)
  - C FFI exports
  - Buffer management
  - Only compiles for native targets
```

### no_std Rules

| Rule | Detail |
|------|--------|
| No std library | Core and zone crates use `#![no_std]` |
| No floating point | All math uses i32/u32/i64/u64 integer operations |
| No system calls | No file I/O, no networking, no threading |
| No panic strings | Use `core::panic` with no format strings in release builds |
| Alloc allowed in zone | `extern crate alloc` for Vec, Box in zone generator |
| No alloc in core | Core crate is fully stack-allocated |

### Forbidden Operations

| Operation | Reason | Alternative |
|-----------|--------|-------------|
| `std::fs` | No filesystem in WASM | Input via function parameters |
| `std::time` | No system clock in WASM | Seed provided externally |
| `std::thread` | No threads in WASM | Single-threaded generation |
| `f32` / `f64` | Non-deterministic across platforms | Integer fixed-point math |
| `println!` / `eprintln!` | No stdout in WASM | Return error codes |
| `String` / `format!` | Requires std | Static byte slices for error context |

## WASM Build Configuration

### Cargo.toml

```toml
[lib]
crate-type = ["cdylib"]  # For WASM module output

[profile.release]
opt-level = "s"          # Optimize for size
lto = true               # Link-time optimization
codegen-units = 1        # Single codegen unit for max optimization
panic = "abort"          # No unwinding in WASM
strip = true             # Strip debug symbols

[target.wasm32-unknown-unknown]
# No additional dependencies needed
```

### Build Commands

```bash
# Build for WASM
rustup target add wasm32-unknown-unknown
cargo build --target wasm32-unknown-unknown --release -p eots-procgen-zone

# Optimize WASM binary
wasm-opt -Os target/wasm32-unknown-unknown/release/eots_procgen_zone.wasm \
  -o eots_procgen_zone.opt.wasm

# Generate JS bindings (for web)
wasm-bindgen target/wasm32-unknown-unknown/release/eots_procgen_zone.wasm \
  --out-dir web/pkg --target web
```

### WASM Exports

```rust
// wasm_bindings.rs (separate from FFI, WASM-only)
#[cfg(target_arch = "wasm32")]
use wasm_bindgen::prelude::*;

#[cfg(target_arch = "wasm32")]
#[wasm_bindgen]
pub fn generate_zone_wasm(zone_id: u8, seed: u64) -> Vec<u8> {
    let mut output = alloc::vec![0u8; 65536];
    let length = generate_zone_internal(zone_id, seed, &mut output);
    output.truncate(length);
    output
}

#[cfg(target_arch = "wasm32")]
#[wasm_bindgen]
pub fn version() -> u32 {
    crate::VERSION
}
```

## Memory Constraints

### WASM Memory Limits

| Parameter | Limit | Rationale |
|-----------|-------|-----------|
| Initial memory | 1 MB | Minimum for zone generation |
| Maximum memory | 4 MB | Browser memory budget for spectator |
| Stack size | 64 KB | Sufficient for recursive generation |
| Heap (alloc) | Up to 3.9 MB | Room data, edge data, validation buffers |

### Memory Usage Per Operation

| Operation | Memory Usage | Lifetime |
|-----------|-------------|----------|
| Zone graph (25 rooms) | ~8 KB | Generation call |
| Validation buffers | ~4 KB | Validation phase |
| Output serialization | ~4 KB | Serialization call |
| PRNG state | 32 bytes | Per-generator instance |
| Total peak | ~20 KB | Single generation call |

### Memory Safety

| Measure | Detail |
|---------|--------|
| No raw pointers in core | All memory managed via Rust ownership |
| Bounded allocations | Vec capacity capped at MAX_ROOMS (30) * sizeof(RoomData) |
| No global mutable state | All state passed via function parameters |
| Stack depth | Maximum recursion depth: 12 (critical path length cap) |

## Integration Path

### Web Spectator Architecture

```
Browser Client
  |
  +-- WASM Module (eots_procgen_zone.wasm)
  |     |
  |     +-- Receives seed from spectator server via WebSocket
  |     +-- Generates zone graph locally (deterministic)
  |     +-- Returns room layout for minimap rendering
  |
  +-- JavaScript UI
        |
        +-- Canvas-based minimap
        +-- Player position overlay (from server events)
        +-- Real-time spectator view
```

### Server-Side Validation

```
Game Server (Rust native binary)
  |
  +-- Primary generation: native eots_procgen.dll
  |
  +-- Validation mirror: WASM module in wasmtime runtime
        |
        +-- Same seed, same parameters
        +-- Compare output hash: must match
        +-- Detects any platform-specific generation drift
```

### Mobile Companion

| Component | Technology |
|-----------|-----------|
| WASM runtime | WebKit (iOS) / V8 (Android) via WebView |
| Data transport | WebSocket to game server |
| UI | Native mobile UI consuming WASM output |
| Features | Map view, party status, Community Tree contribution |

## Testing

### WASM-Specific Tests

| Test | Purpose |
|------|---------|
| `wasm_native_parity` | Generate 1,000 zones in both WASM and native. Compare byte-for-byte. |
| `wasm_memory_cap` | Generate largest possible zone (30 rooms). Verify peak memory < 4 MB. |
| `wasm_performance` | Generate 100 zones. Verify < 200ms total (avg < 2ms per zone). |
| `wasm_no_panic` | Fuzz 10,000 random seeds. No panics (abort behavior verified). |
| `wasm_binary_size` | Verify optimized .wasm file < 200 KB. |

### CI Pipeline

```
1. Build native (x86_64-pc-windows-msvc, x86_64-unknown-linux-gnu)
2. Build WASM (wasm32-unknown-unknown)
3. Run native tests (cargo test)
4. Run WASM tests via wasmtime (cargo test --target wasm32-unknown-unknown)
5. Parity check: compare 1,000 seeds native vs WASM
6. Size check: .wasm < 200 KB
7. Memory check: peak memory < 4 MB
```

## Performance Targets

| Metric | Native Target | WASM Target | Notes |
|--------|-------------|-------------|-------|
| Single zone generation | < 50ms | < 100ms | 2x overhead acceptable for WASM |
| Binary size | N/A (dynamic lib) | < 200 KB (.wasm) | After wasm-opt |
| Peak memory | < 1 MB | < 4 MB | WASM has higher allocator overhead |
| Startup time | N/A (loaded at boot) | < 50ms | WASM module instantiation |

## Implementation Notes

- `no_std` enforcement via `#![no_std]` at crate root with CI lint check
- WASM bindings in `eots-procgen-zone/src/wasm_bindings.rs` behind `#[cfg(target_arch = "wasm32")]`
- wasm-bindgen used for JavaScript interop (web spectator)
- wasmtime used for server-side validation (Rust native host running WASM guest)
- wasm-opt from binaryen toolkit integrated into build script
- WASM binary committed to `web/pkg/` for web spectator deployment
- Feature flags: `default = ["native"]`, `wasm = ["wasm-bindgen"]`
- Float-free verification: CI runs `cargo clippy` with custom lint denying `f32`/`f64` usage in core/zone crates
