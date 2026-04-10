# 13 — TECHNICAL ARCHITECTURE & ENGINE STRATEGY

## Stack Overview
| Layer | Technology | Rationale |
|---|---|---|
| Engine | Unreal Engine 5.5 | Nanite for parallax, mature tooling, console export |
| Simulation | Custom C++20 ECS (standalone) | Determinism requirement precludes UE5 gameplay framework |
| Math | FixedPoint64 (32.32 int64) | Cross-platform rollback determinism |
| Rendering Bridge | EotSSimulationSubsystem + RenderProxy | Decouples sim (60Hz fixed) from render (variable FPS) |
| Proc-Gen | Rust (no_std) → C FFI + WASM | Performance + portability + determinism |
| Audio | FMOD Studio + custom C++ DSP | Real-time voice processing requirement |
| Backend Persistent | Supabase (PostgreSQL) | Community Tree, accounts, contributions |
| Backend Session | Redis 7+ | Matchmaking, presence, infection sync, fusion state |
| Session Service | Rust gRPC server | Wraps Redis operations for game server consumption |
| Analytics | Mixpanel + custom telemetry | A/B testing, balance tuning |

## Architecture Boundaries

```
┌─────────────────────────────────────────────────┐
│                UNREAL ENGINE 5.5                 │
│  ┌──────────────┐  ┌──────────────┐  ┌────────┐ │
│  │   Rendering   │  │  Input (EIS)  │  │  FMOD  │ │
│  └──────┬───────┘  └──────┬───────┘  └───┬────┘ │
│         │                 │              │       │
│  ┌──────▼─────────────────▼──────────────▼────┐  │
│  │        EotS BRIDGE LAYER (Subsystem)       │  │
│  │  RenderProxy | InputBridge | AudioBridge   │  │
│  └──────────────────┬────────────────────────┘  │
│                     │ (reads/writes)             │
│  ┌──────────────────▼────────────────────────┐  │
│  │         CUSTOM ECS (C++20, standalone)      │  │
│  │  World | Systems | Components | Scheduler   │  │
│  │  FixedPoint64 math | 60Hz deterministic     │  │
│  │  Serializable for rollback snapshots        │  │
│  └──────────────────┬────────────────────────┘  │
│                     │ (network I/O)              │
│  ┌──────────────────▼────────────────────────┐  │
│  │         ROLLBACK NETCODE LAYER             │  │
│  │  InputBuffer | SnapshotRing | Transport    │  │
│  └──────────────────┬────────────────────────┘  │
│                     │ (UDP)                      │
└─────────────────────┼───────────────────────────┘
                      │
        ┌─────────────▼──────────────┐
        │     DEDICATED SERVER        │
        │  (Authoritative ECS sim)    │
        │  + Redis client             │
        │  + Supabase client          │
        └────────────────────────────┘
```

## Key Invariants
1. NO floating-point math in the ECS. FixedPoint64 only. Floats exist only in the render bridge.
2. The ECS has ZERO Unreal headers. It compiles as standalone C++20.
3. Snapshot serialization of 500 entities must complete in < 500 μs.
4. The rollback window is 10 ticks (166 ms). Beyond that: full state resync from server.
5. All ECS components are trivially copyable (no heap allocations, no pointers).
6. The Rust proc-gen module produces identical output for identical seeds across x86-64, ARM64, and WASM.
7. The PRNG (xoshiro256**) in both C++ and Rust produces bit-identical sequences for identical seeds.
