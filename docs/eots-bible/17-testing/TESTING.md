# 17 — TESTING, INSTRUMENTATION & QUALITY GATES

## Test Layers

### Layer 1: Unit Tests (Automated, CI)
| System | Test Count (Target) | Key Assertions |
|---|---|---|
| FixedPoint64 math | 256+ golden reference tests | Bit-identical to Python decimal reference |
| ECS CRUD | 50+ | Entity lifecycle, component add/remove, ForEach correctness |
| ECS serialization | 20+ | Serialize → deserialize → re-serialize produces identical bytes |
| Rollback snapshot | 10+ | Snapshot at tick T, modify, restore, verify match |
| Rust proc-gen | 8000+ (1000 per zone config) | All generated graphs pass 5 structural constraints |
| Rust RNG | 10 | xoshiro256** output matches C++ output for same seeds |
| Damage calculation | 50+ | Integer-only pipeline, known input/output pairs |
| Infection tier transitions | 20+ | Correct thresholds, correct buff values |

### Layer 2: Integration Tests (Automated, CI)
| Test | Description | Frequency |
|---|---|---|
| Determinism check | Run 2 ECS worlds side-by-side with identical inputs for 600 ticks. Assert identical checksums. | Every CI run |
| Rollback simulation | 2 worlds with artificial input delay. Verify sync after rollback. | Every CI run |
| Fusion lifecycle | Simulate 2-player fusion request → merge → fused → separate. Verify state machines match. | Every CI run |
| Zone spawn | Generate all 8 zone configs, spawn into UE5, verify room counts and connectivity. | Nightly |
| Community Tree | Contribute Echoes via Supabase function, verify unlock logic, verify prerequisite enforcement. | Nightly |

### Layer 3: Playtest Quality Gates
| Milestone | Gate Criteria | Method |
|---|---|---|
| Vertical Slice (Month 10) | 2 zones playable, Fusion works, Spore-Talk audible, 15-minute run completable | Internal playtest (team) |
| Alpha (Month 16) | All 8 zones, all bosses defeatable, Community Tree functional, Heat 10 clearable | Internal + friends-and-family (50 players) |
| Closed Beta (Month 18) | 10K players, server stability at 1000 CCU, desync rate <1%, crash rate <0.5% | External beta |
| Open Beta (Month 22) | 50K players, all platform builds stable, matchmaking <30s, console cert pass | Public beta |
| Launch (Month 24) | Metacritic 82+ target, Steam positive rate >80%, no P0 bugs | Gold master |

### Layer 4: Live Monitoring
| Metric | Alert Threshold | Response |
|---|---|---|
| Desync rate | >2% of sessions | Investigate netcode, deploy hotfix |
| Crash rate | >0.5% of sessions | P0 bug triage |
| Matchmaking time | >60s median | Scale server fleet |
| Community Tree contribution rate | <50% of projection | Adjust Echo drop rates |
| DAU/MAU | <25% (30 days post-launch) | Content acceleration, community outreach |

## Instrumentation
Every session logs:
- Session ID, player IDs, zone, seed, duration, outcome (clear/death/disconnect)
- Echo Boons selected (per player)
- Infection level at end
- Fusion events (time, duration, class)
- Death causes (enemy type, room ID, tick number)
- Desync events (tick, checksum mismatch)
- Voice activity (duration of Spore-Talk usage, infection level during)
