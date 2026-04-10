# 28 — OPEN QUESTIONS & UNRESOLVED ASSUMPTIONS

| ID | Question | Impact | Deadline | Owner |
|---|---|---|---|---|
| Q01 | What is the maximum server population for Community Tree to feel meaningful? 1K? 5K? 10K? | Tree pacing, server infrastructure costs | Month 3 (pre-VS playtest) | Lead Designer |
| Q02 | Should the Mycelium Narrator be a single voice actor or multiple voices representing different civilizations? | Audio budget, recording schedule, narrative tone | Month 4 (casting decision) | Audio Director |
| Q03 | How do we handle server merges when Community Trees are at different stages? Take max? Average? Player vote? | Player experience, data integrity | Month 12 (before beta server scaling) | Backend Dev + Designer |
| Q04 | Should Spore Plan (cooperative Turn()) be available in solo play or Fusion-exclusive? | Solo player experience, design purity | Month 6 (after Fusion prototype) | Lead Designer |
| Q05 | What is the minimum viable narrator line count for launch? 400? 600? 900? | Audio budget, recording time, narrative coverage | Month 8 (script completion) | Narrative Designer |
| Q06 | Should the True Ending vote require a minimum participation threshold (e.g., 50% of active players must vote)? | Narrative stakes, fairness for casual players | Month 14 (Lore branch implementation) | Lead Designer |
| Q07 | How aggressive should the AI Director be in Zone 8? Target failure rate: 30%? 40%? 50%? | Difficulty balance, player frustration | Month 16 (Alpha balance pass) | Lead Designer |
| Q08 | Should the Ecosystem simulation (Rain World food chain) be in the MVP or deferred to post-launch? | Scope, engineering complexity, uniqueness value | Month 4 (scope lock) | Producer + Tech Director |
| Q09 | What is the refund policy if a player purchases cosmetics on a server that later merges? | Customer satisfaction, legal compliance | Month 18 (before store launch) | Producer + Legal |
| Q10 | Should cross-play be PC+Console at launch or PC-only with console cross-play added post-launch? | Player population, certification complexity | Month 12 (console porting start) | Tech Director |

## Assumptions (Require Validation)

| ID | Assumption | Validation Method | Deadline |
|---|---|---|---|
| A01 | Players will find Fungal Fusion fun, not frustrating | Vertical Slice playtest with 20 external players | Month 10 |
| A02 | Spore-Talk voice distortion is funny, not annoying | Playtest + survey (Likert scale on enjoyment) | Month 10 |
| A03 | Community Tree pacing feels rewarding at 1000-player servers | Simulation + closed beta telemetry | Month 18 |
| A04 | FixedPoint64 math is fast enough for 500-entity snapshots in <500μs | Benchmark on target hardware (min spec PC) | Month 6 |
| A05 | The $29.99 price point does not suppress Day 1 sales below 500K units | Market analysis + comparable data (Mewgenics @ $29.99 = 1M week 1) | Pre-launch |
| A06 | 60Hz tick rate with 10-tick rollback window is sufficient for 4-player sessions at <100ms RTT | Network simulation + closed beta telemetry | Month 18 |
