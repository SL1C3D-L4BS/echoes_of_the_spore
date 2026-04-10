# ECHOES OF THE SPORE — MASTER GAME BIBLE

## Production-Grade Technical Design Library v2026.1

**Studio:** SL1C3D-L4BS
**Lead Architect:** Michael A. Cooper
**Date:** April 2026
**Classification:** CONFIDENTIAL

---

## Library Structure

This library contains 31 standalone documents organized by subsystem. Each document is self-contained but cross-references related sections. The numbering defines recommended reading order.

| # | Section | File | Status |
|---|---|---|---|
| 00 | Executive Vision & Product Thesis | `00-executive/VISION.md` | Complete |
| 01 | Market Positioning & Differentiation | `01-market/MARKET.md` | Complete |
| 02 | Core Game Loop & Player Fantasy | `02-core-loop/CORE_LOOP.md` | Complete |
| 03 | World Architecture & Biome System | `03-world/WORLD.md` | Complete |
| 04 | Combat Architecture & Feel Targets | `04-combat/COMBAT.md` | Complete |
| 05 | Class Design, Loadouts & Build System | `05-classes/CLASSES.md` | Complete |
| 06 | Progression, Persistence & Meta | `06-progression/PROGRESSION.md` | Complete |
| 07 | Multiplayer, Fusion & Social Systems | `07-multiplayer/MULTIPLAYER.md` | Complete |
| 08 | AI, Enemy Taxonomy & Boss Logic | `08-ai-enemies/AI_ENEMIES.md` | Complete |
| 09 | Narrative, Lore & Environmental Storytelling | `09-narrative/NARRATIVE.md` | Complete |
| 10 | Art Direction & Visual Language | `10-art-direction/ART_DIRECTION.md` | Complete |
| 11 | UI/UX, Input & Readability | `11-ui-ux/UI_UX.md` | Complete |
| 12 | Audio Direction, Narration & VOIP | `12-audio/AUDIO.md` | Complete |
| 13 | Technical Architecture & Engine Strategy | `13-tech-architecture/TECH_ARCH.md` | Complete |
| 14 | Networking, Determinism & Rollback | `14-networking/NETCODE.md` | Complete |
| 15 | Procedural Generation & Content Validation | `15-procgen/PROCGEN.md` | Complete |
| 16 | Tooling, Build System & Dev Workflow | `16-tooling/TOOLING.md` | Complete |
| 17 | Testing, Instrumentation & Quality Gates | `17-testing/TESTING.md` | Complete |
| 18 | Performance Budgets & Platform Targets | `18-performance/PERFORMANCE.md` | Complete |
| 19 | Production Roadmap & Milestone Plan | `19-production/PRODUCTION.md` | Complete |
| 20 | Risk Register & Contingency Planning | `20-risk/RISK.md` | Complete |
| 21 | Monetization & Ethical Constraints | `21-monetization/MONETIZATION.md` | Complete |
| 22 | Accessibility & Inclusive Design | `22-accessibility/ACCESSIBILITY.md` | Complete |
| 23 | Live Ops, Telemetry & Content Scaling | `23-live-ops/LIVE_OPS.md` | Complete |
| 24 | Data Schemas & System Interfaces | `24-data-schemas/SCHEMAS.md` | Complete |
| 25 | Generative Prompt Library | `25-prompt-library/PROMPTS.md` | Complete |
| 26 | Asset Pipeline & Production Workflow | `26-asset-pipeline/ASSET_PIPELINE.md` | Complete |
| 27 | Decision Log | `27-decision-log/DECISIONS.md` | Complete |
| 28 | Open Questions & Unresolved Assumptions | `28-open-questions/OPEN_QUESTIONS.md` | Complete |
| 29 | Subsystem Dependency Map | `29-dependency-map/DEPENDENCIES.md` | Complete |
| 30 | Changelog from Original Bible | `30-changelog/CHANGELOG.md` | Complete |

## Hard Constraints (Apply Everywhere)

| Constraint | Value | Rationale |
|---|---|---|
| Simulation tick rate | 60 Hz fixed | Rollback determinism |
| Simulation math | FixedPoint64 (32.32 int64) only | Cross-platform determinism |
| Max entities per room | 4,096 | ECS archetype storage bound |
| Snapshot serialize time | < 500 μs for 500 entities | Rollback re-simulation budget |
| Network latency target | < 50 ms round-trip | 4P fusion playability |
| Rollback window | 10 ticks (166 ms) | Re-simulation cost ceiling |
| Max players per session | 4 | Fusion design constraint |
| Max players per server | 10,000 | Community Tree scale |
| Render FPS | Variable (target 60+) | Interpolation from sim ticks |
| Audio DSP frame | 20 ms (960 samples @ 48 kHz) | Opus codec alignment |
| Room generation time | < 100 ms | Load screen budget |

## Naming Conventions

| Domain | Convention | Example |
|---|---|---|
| ECS Components | PascalCase + Component | `TransformComponent` |
| ECS Systems | PascalCase + System | `MovementSystem` |
| UE5 Classes | EotS prefix + PascalCase | `EotSGameInstance` |
| Rust Crates | eots-procgen-{module} | `eots-procgen-core` |
| Redis Keys | entity:{id}:field | `session:abc123:infection:p1` |
| Supabase Tables | snake_case | `skill_tree_nodes` |
| Asset Files | Zone_Type_Variant_## | `Sporehaven_Combat_Template_01` |
