# Milestone Definitions

## Milestone Map

| ID | Name | Month | Duration | Key Deliverable |
|----|------|-------|----------|----------------|
| M1 | Project Kickoff | 1 | 1 month | Team hired, tools set up |
| M2 | Tech Foundation | 2 | 1 month | ECS + FixedPoint64 + URP 2D |
| M3 | Core Loop Prototype | 3 | 1 month | Movement + basic combat |
| M4 | Art Style Lock | 4 | 1 month | Visual direction finalized |
| M5 | Netcode Prototype | 5 | 1 month | Rollback working (2 players) |
| M6 | Proc-Gen v1 | 6 | 1 month | Rust proc-gen generating zones |
| M7 | Combat Polish | 7 | 1 month | All 4 classes playable |
| M8 | Fusion Prototype | 8 | 1 month | 2-player fusion working |
| M9 | Audio Prototype | 9 | 1 month | FMOD + Spore-Talk DSP |
| M10 | Vertical Slice | 10 | 1 month | 2 zones, full run, online co-op |
| M11-12 | Content Prod 1 | 11-12 | 2 months | Zones 3-5, bosses, boons |
| M13-14 | Content Prod 2 | 13-14 | 2 months | Zones 6-8, all classes fused |
| M15 | Community Tree | 15 | 1 month | Backend + tree + unlocks |
| M16 | Alpha | 16 | 1 month | All content, internal alpha |
| M17 | Closed Beta Prep | 17 | 1 month | Server scaling, analytics |
| M18 | Closed Beta | 18 | 1 month | 10K players |
| M19 | Console Ports | 19 | 1 month | PS5 + Xbox + Switch builds |
| M20 | Open Beta | 20 | 1 month | 50K players |
| M21-22 | Polish | 21-22 | 2 months | Bugs, perf, accessibility |
| M23 | Gold Candidate | 23 | 1 month | Release candidate |
| M24 | Launch | 24 | 1 month | Ship + Season 1 |

## Milestone Exit Criteria

### M4: Art Style Lock

| Criterion | Metric |
|-----------|--------|
| Character sprites (1 class) complete | 100% of animations |
| Zone 1 tileset complete | All tiles + palette |
| UI mockups approved | Main HUD + menus |
| Shader prototypes working | URP 2D + custom effects |
| Art bible documented | Colors, proportions, style guide |

### M10: Vertical Slice

| Criterion | Metric |
|-----------|--------|
| 2 zones playable start to finish | Both with boss |
| All 4 classes playable (basic moveset) | Core abilities working |
| Fusion merge/separate functional | 2-player tested |
| Spore-Talk DSP audible | All 6 tiers working |
| Online co-op (2-4 players) | Over internet, < 150ms RTT |
| Rollback netcode | < 2% desync rate |
| Boon system functional | 10+ boons selectable |
| Session length | 15-20 minutes per run |
| Internal team approval | 80% rate "fun" in survey |

### M16: Alpha

| Criterion | Metric |
|-----------|--------|
| All 8 zones complete | Art, enemies, bosses |
| All 4 classes complete | Full movesets |
| All 12 fusion pairs | Unique moves per pair |
| Community Tree functional | Backend + frontend |
| Heat system (0-10) | All modifiers working |
| 50+ boons | All rarities represented |
| Narrator VO (placeholder) | 100+ lines recorded |
| Crash rate | < 2% |
| Session completion rate | > 40% |

### M18: Closed Beta

| Criterion | Metric |
|-----------|--------|
| 10K concurrent users | Server infrastructure holds |
| Matchmaking | < 30s P95 |
| Desync rate | < 1% |
| Crash rate | < 0.5% |
| D1 retention | > 35% |
| Average session length | > 20 min |
| All P0 bugs fixed | 0 open P0 |

### M24: Launch

| Criterion | Metric |
|-----------|--------|
| All platforms certified | PS5, Xbox, Switch, PC |
| Localization complete (10 languages) | All text + 5-language VO |
| Crash rate | < 0.1% |
| Desync rate | < 0.05% |
| Metacritic target | 82+ |
| Steam reviews | > 80% positive |
| Zero P0 bugs | 0 open |
| Season 1 content ready | Battle pass, new boons |
| Marketing campaign live | Trailers, press, streamers |
| Server capacity | 20K CCU minimum |

## Milestone Review Process

| Step | Timing | Participants |
|------|--------|-------------|
| Feature freeze | 3 days before milestone | All developers |
| QA blitz | 2 days before milestone | QA team + volunteers |
| Bug triage | 1 day before milestone | Leads + Producer |
| Demo build | Milestone day | Build engineer |
| Milestone review meeting | Milestone day | Full team + stakeholders |
| Go/No-Go decision | Milestone day | Producer + Director |
| Retrospective | Day after milestone | Full team |

## Risk Per Milestone

| Milestone | Highest Risk | Mitigation |
|-----------|-------------|-----------|
| M5 (Netcode) | Rollback complexity | Prototype early, 2-week spike |
| M8 (Fusion) | State machine complexity | Comprehensive test suite |
| M10 (VS) | Integration of all systems | 2-week integration buffer |
| M18 (Beta) | Server scaling | Load test at M17 |
| M19 (Console) | Cert requirements | Start cert prep at M17 |
| M24 (Launch) | Critical bugs | 1-month polish buffer |
