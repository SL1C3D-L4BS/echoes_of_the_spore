# 17 — TESTING, INSTRUMENTATION & QUALITY GATES

## Test Layers

### Layer 1: Unit Tests (Unity Test Runner — EditMode)
All pure C# ECS/Math/Netcode tests run in EditMode (no Play required).

| System | Test Count | Key Assertions |
|---|---|---|
| FixedPoint64 math | 256+ | Bit-identical to Python decimal golden reference |
| FixedVec2 operations | 50+ | Normalize, dot, cross, length, distance |
| ECS CRUD | 50+ | Entity lifecycle, component add/remove, ForEach |
| ECS serialization | 20+ | Serialize → deserialize → re-serialize = identical bytes |
| Rollback snapshot | 10+ | Snapshot tick T, modify, restore, verify match |
| Damage calculation | 50+ | Integer-only pipeline, known I/O pairs |
| Infection transitions | 20+ | Correct thresholds, correct buff values |
| Fusion state machine | 15+ | All state transitions with tick-precise timing |
| Rust RNG (via FFI) | 10 | xoshiro256** matches C# output for same seeds |

**Critical:** ECS tests use ZERO UnityEngine.* imports. They are pure C# and could compile outside Unity.

### Layer 2: Integration Tests (Unity Test Runner — PlayMode)
| Test | Description | Frequency |
|---|---|---|
| Determinism | 2 EotSWorld instances, identical inputs, 600 ticks, assert identical checksums | Every CI |
| Rollback | 2 worlds with artificial delay, verify sync | Every CI |
| Fusion lifecycle | 2-player merge → fuse → separate, verify state | Every CI |
| Room spawn | Generate zone, instantiate Tilemap prefabs, verify connectivity | Nightly |
| Render proxy | Create ECS entity, verify SpriteRenderer position matches ECS state | Nightly |
| Community Tree | POST to Supabase function, verify unlock logic | Nightly |

### Layer 3: Playtest Gates
| Milestone | Gate | Method |
|---|---|---|
| Vertical Slice (M10) | 2 zones, Fusion works, 15-min run | Internal |
| Alpha (M16) | 8 zones, all bosses, Community Tree, Heat 10 | 50 friends-and-family |
| Closed Beta (M18) | 10K players, 1000 CCU, desync <1%, crash <0.5% | External |
| Open Beta (M22) | 50K, all platforms, matchmake <30s | Public |
| Launch (M24) | Metacritic 82+, Steam positive >80%, zero P0 | Gold master |

### Layer 4: Live Monitoring
| Metric | Alert | Response |
|---|---|---|
| Desync rate | >2% | Netcode hotfix |
| Crash rate | >0.5% | P0 triage |
| Match time | >60s | Scale servers |
| DAU/MAU | <25% (30d) | Content acceleration |
