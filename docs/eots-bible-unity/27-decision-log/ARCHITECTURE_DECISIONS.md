# Architecture Decision Records

## ADR-001: Custom ECS Over Unity DOTS

**Date:** 2026-04-10
**Status:** Accepted
**Decision:** Build a custom ~2,000-line C# ECS instead of using Unity DOTS.

**Context:** Rollback netcode requires deterministic simulation. Unity DOTS uses float math internally (IEEE 754), which produces different rounding on different platforms (x86 vs ARM, different compilers). Our game needs bit-identical simulation for up to 10 ticks of rollback.

**Consequences:**
- (+) Guaranteed determinism with FixedPoint64
- (+) Simple, readable, debuggable code
- (+) Zero Unity dependency in Core/ enables standalone server
- (-) No Burst compiler optimization
- (-) No Jobs system parallelism
- (-) Must implement our own spatial hash, queries, etc.

**Acceptable because:** Max 4,096 entities (2D game), single-threaded ECS is fast enough within 4ms budget.

---

## ADR-002: FixedPoint64 (32.32) Over float

**Date:** 2026-04-10
**Status:** Accepted
**Decision:** Use 32.32 fixed-point (int64) for all simulation math.

**Context:** Cross-platform determinism is non-negotiable for rollback netcode. float/double produce different results on x86 vs ARM, even with strict IEEE 754 mode.

**Alternatives considered:**
- `float` with `StrictMode`: Not deterministic across platforms
- `decimal`: Too slow (software emulation)
- `int` only: Insufficient precision for physics
- **`long` (32.32 fixed)**: Deterministic, fast, sufficient range/precision

---

## ADR-003: Rust for Proc-Gen Over C#

**Date:** 2026-04-10
**Status:** Accepted
**Decision:** Proc-gen engine in Rust (no_std), called via C FFI.

**Context:** Zone generation algorithm is complex (DAG with validation). Rust provides: memory safety without GC, no_std for WASM target (future mobile spectate), and guaranteed determinism with explicit integer math.

**Consequences:**
- (+) Fast, safe, deterministic
- (+) WASM-portable for future web/mobile
- (+) Independent development (Rust dev works in parallel)
- (-) FFI bridge complexity (DllImport, buffer management)
- (-) Two-language codebase

---

## ADR-004: FMOD Over Unity Audio

**Date:** 2026-04-10
**Status:** Accepted
**Decision:** FMOD Studio for all audio.

**Context:** Adaptive music (zone layers, combat intensity), Spore-Talk DSP chain, and spatial VOIP require professional audio middleware.

**Alternatives:** Unity Audio (insufficient DSP), Wwise (more complex, similar cost), custom (too much work).

---

## ADR-005: Unity 6.3 LTS Over Godot 4.6

**Date:** 2026-04-10
**Status:** Accepted
**Decision:** Unity 6.3 LTS as engine.

**Context:** Evaluated Unity, Godot, and Unreal. Unity wins on: proven 2D pipeline (Hollow Knight, Dead Cells), C# language (Claude Code verified fluent), built-in console export, MCP integration verified, no royalties (vs Unreal 5%).

Godot 4.6 considered but rejected: less proven console pipeline, GDExtension C++ adds complexity, smaller ecosystem.

---

## ADR-006: Supabase + Redis Over Custom Backend

**Date:** 2026-04-10
**Status:** Accepted
**Decision:** Supabase (PostgreSQL) for persistence, Redis for real-time state.

**Context:** Community Tree, matchmaking, leaderboards need both persistent storage and real-time caching. Supabase provides: hosted Postgres, auth, RLS, real-time subscriptions. Redis provides: sorted sets for matchmaking, pub/sub for live updates, low-latency caching.

**Alternative:** Custom Node.js/Go backend — rejected (unnecessary complexity for indie team).
