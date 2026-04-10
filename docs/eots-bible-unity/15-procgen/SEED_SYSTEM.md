# Seed System

## Overview

The seed system ensures deterministic procedural generation across all clients. A single server seed cascades into zone seeds, room seeds, and content seeds through a derivation chain. Identical seeds always produce identical results on any platform.

## Seed Hierarchy

```
Server Seed (u64)
  |
  +-- Zone Seed = derive(server_seed, zone_id)
        |
        +-- Room Seed = derive(zone_seed, room_index)
        |     |
        |     +-- Tile Seed = derive(room_seed, 0x01)
        |     +-- Enemy Seed = derive(room_seed, 0x02)
        |     +-- Hazard Seed = derive(room_seed, 0x03)
        |     +-- Loot Seed = derive(room_seed, 0x04)
        |
        +-- Graph Seed = derive(zone_seed, 0xFF)
              (used for zone graph structure itself)
```

## Derivation Function

```rust
fn derive(parent_seed: u64, salt: u64) -> u64 {
    let mut hasher = Xoshiro256StarStar::new(parent_seed);
    // Advance RNG by salt value to create unique stream
    for _ in 0..salt.wrapping_add(1) {
        hasher.next_u64();
    }
    hasher.next_u64()
}
```

### Properties

| Property | Guarantee |
|----------|----------|
| Deterministic | Same parent + salt always produces same child seed |
| Independent | Different salts produce statistically independent streams |
| Platform-identical | Rust and C# implementations produce bit-identical output |
| Collision-resistant | u64 space (1.8x10^19 values) makes collisions negligible |

## Server Seed Generation

| Parameter | Value |
|-----------|-------|
| Source | Server system clock at delve creation (nanosecond precision) |
| Type | u64 |
| Storage | Delve session record in server database |
| Broadcast | Sent to all clients during matchmaking handshake |
| Persistence | Stored with delve results for replay/debug |

### Special Seeds

| Seed Value | Purpose |
|------------|---------|
| 0 | Reserved (invalid, triggers re-generation) |
| 1-999 | Reserved for automated testing |
| 1000-9999 | Reserved for community challenges (curated seeds) |
| 10000+ | Normal generation range |

## Zone Seed Derivation

```rust
let zone_seed = derive(server_seed, zone_id as u64);
```

| Zone | Salt | Purpose |
|------|------|---------|
| 1 | 1 | Sporehaven zone graph + rooms |
| 2 | 2 | Cortex zone graph + rooms |
| 3 | 3 | Ferment Pits |
| 4 | 4 | Chitinhall |
| 5 | 5 | Rot Garden |
| 6 | 6 | Myco-Forge |
| 7 | 7 | Necroveil |
| 8 | 8 | Necrospore Core |

Each zone seed feeds into the zone graph algorithm (Phase 1-5) and then into per-room seed derivation.

## Room Seed Derivation

```rust
let room_seed = derive(zone_seed, room_index as u64);
```

Room seeds are used for all per-room randomization: tile layout, enemy composition, hazard placement, loot drops. Sub-seeds for each system prevent one system's randomization from affecting another.

### Sub-Seed Salts

| Salt | System | Usage |
|------|--------|-------|
| 0x01 | Tile layout | Platform placement, decoration, terrain variation |
| 0x02 | Enemy spawning | Enemy types, positions, facing, activation timing |
| 0x03 | Hazard placement | Hazard types, positions, timing patterns |
| 0x04 | Loot generation | Drop tables, rarity rolls, item selection |
| 0x05 | Visual variation | Prop placement, lighting variation, parallax offsets |
| 0x06 | Puzzle parameters | Puzzle solution, obstacle configuration |

## Determinism Guarantee

### Requirements

| Requirement | Detail |
|------------|--------|
| Cross-platform | Windows, Linux, macOS, consoles produce identical output for same seed |
| Cross-version | Seed behavior locked per major version. Patch versions must not change generation. |
| Cross-language | Rust and C# PRNG implementations are bit-identical |
| Floating-point | No floating-point operations in generation pipeline (integer math only) |
| No system calls | No time-dependent or OS-dependent operations during generation |

### Verification

| Test | Method |
|------|--------|
| Rust unit tests | Generate 10,000 zones, hash output, compare against golden file |
| C# PRNG parity | Generate 10,000 random values in both Rust and C#, assert all identical |
| Cross-platform CI | Run generation on Windows + Linux + macOS, compare binary output |
| Regression test | Every patch: re-generate 100 curated seeds, diff against known-good output |

## Seed Sharing

### Community Challenges

Seeds can be shared between players for community events:

| Feature | Detail |
|---------|--------|
| Seed display | Show current delve seed in pause menu and results screen |
| Seed input | Settings > Gameplay > Custom Seed field (solo mode only) |
| Leaderboard | Community challenge seeds have dedicated leaderboards |
| Sharing format | 12-character alphanumeric code (base62 encoding of u64) |

### Encoding

```
u64 seed -> base62 string (a-z, A-Z, 0-9)
Example: 847293651834 -> "b3Kf9mQ2"
```

| Parameter | Value |
|-----------|-------|
| Code length | 8-12 characters |
| Character set | a-z, A-Z, 0-9 (62 characters) |
| Validation | Decode and verify seed is in valid range (10000+) |
| Display | Monospace font (Space Mono) for readability |

### Restrictions

| Rule | Detail |
|------|--------|
| Custom seeds in solo only | Multiplayer always uses server-generated seed |
| No seed manipulation | Client cannot influence server seed selection |
| Seed logging | All seeds logged server-side for anti-cheat analysis |
| Curated seeds | Dev team can push curated seeds for weekly challenges via server config |

## Implementation Notes

- Seed derivation in `eots-procgen-core/src/seed.rs`
- PRNG implementation in `eots-procgen-core/src/rng.rs` (Xoshiro256**)
- C# mirror PRNG in `Assets/_Project/Scripts/ProcGen/Xoshiro256.cs`
- Seed display UI in `PauseMenuController` and `ResultsScreenController`
- Custom seed input validated by `SeedValidationService` before passing to `ProcGenBridge`
- Base62 encoding/decoding in `SeedEncoder` utility class (both Rust and C#)
- Community challenge seeds stored in server config, fetched on hub entry
- Seed regression tests run as part of CI pipeline on every commit to procgen crate
