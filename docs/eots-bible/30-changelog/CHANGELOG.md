# 30 — CHANGELOG FROM ORIGINAL BIBLE

## What Changed: Original Bible (OMEGA-1.0) → Master Bible (v2026.1)

### Structure
- **BEFORE:** Single 690-line monolithic document
- **AFTER:** 31-file library organized by subsystem, each standalone and cross-referenced

### Added (Did Not Exist in Original)
| Section | Rationale |
|---|---|
| 02 - Core Game Loop | Original had no explicit loop definition or session flow |
| 08 - AI, Enemies, Bosses | Original had zero enemy AI specification |
| 11 - UI/UX | Original had no HUD layout, input mapping, or menu flow |
| 16 - Tooling | Original had no CI/CD, build targets, or custom tools |
| 17 - Testing | Original had no test layers, quality gates, or acceptance criteria |
| 18 - Performance | Original had no frame budgets, memory budgets, or platform targets |
| 19 - Production | Original had minimal staffing plan, no role breakdown |
| 20 - Risk | Original had no risk register, no MoSCoW prioritization, no scope control |
| 22 - Accessibility | Original had zero accessibility consideration |
| 23 - Live Ops | Original had no seasonal calendar, no telemetry specification, no balance process |
| 24 - Data Schemas | Original had no ECS component definitions, no Supabase/Redis schemas |
| 27 - Decision Log | Original made decisions without documenting alternatives |
| 28 - Open Questions | Original did not flag uncertain assumptions |
| 29 - Dependency Map | Original had no build order or system dependency graph |

### Upgraded (Existed But Were Weak)
| Section | What Changed |
|---|---|
| 03 - World | Added per-zone detail specs: room counts, enemy tiers, music keys, estimated clear times, unique mechanics |
| 04 - Combat | Added quantified feel targets (frame counts), damage model formula, combo system, healing resource |
| 05 - Classes | Added slot layouts per Crest, full Function table for Sporeling (example), expanded Fused Class descriptions |
| 07 - Multiplayer | Added Fusion state machine with exact tick counts, Spore Plan planning budget, Rite format details |
| 09 - Narrative | Added narrator line budget breakdown by category with exact counts |
| 19 - Production | Added role-by-role staffing plan for each phase |
| 21 - Monetization | Added explicit anti-predatory principles (7 rules) |

### Resolved Contradictions
| Issue | Resolution |
|---|---|
| Original claimed "3-5M units Year 1" as target but also "$180M-$300M revenue" — the low end (3M × $29.99 = $90M) doesn't reach $180M without aggressive pass/cosmetic attach rates | Separated conservative ($150M gross) and aggressive ($290M gross) scenarios with explicit assumptions for each |
| Original listed 8 civilizations + Resonants but Resonants were ambiguously "not a civilization" while still providing boons | Clarified: Resonants ARE a boon source (functionally 9th civilization for gameplay) but are narratively distinct (the Mycelium's own cognitive process) |
| Original listed "250K+ CCU" as target which would require $50K+/month in server costs at scale | Added server cost to budget (3% = $300K) and added server merge lifecycle in Live Ops section |

### Scope Control (New)
- Added MoSCoW prioritization (Section 20) explicitly identifying what can be cut:
  - MUST HAVE: 4 zones, 6 fused classes, 4 civilizations, 2P co-op, basic Spore-Talk
  - SHOULD HAVE: all 8 zones, 12 fused classes, 8 civs, 4P, full DSP, narrator
  - COULD HAVE: Rite PvP, ecosystem sim, Hunters, Codex, Wish Board
  - WON'T HAVE: native mobile, VR, ranked PvP, UGC/modding
