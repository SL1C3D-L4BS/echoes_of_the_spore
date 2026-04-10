# RISK ASSESSMENT (EXECUTIVE LEVEL)

## Risk Matrix Overview

```
              LOW IMPACT     MEDIUM IMPACT    HIGH IMPACT     CRITICAL IMPACT
HIGH PROB   │              │ R03 Tree Pacing │ R09 Scope      │                │
            │              │                 │ Creep          │                │
MED PROB    │              │ R04 Console     │ R02 GenAI      │ R01 Netcode    │
            │              │ R08 VOIP        │ R10 Personnel  │ R07 FP64 Bugs  │
            │              │ R11 Infra Cost  │                │ R12 Fusion     │
LOW PROB    │              │ R06 Saturation  │ R05 Creator     │                │
            │              │                 │ Adoption       │                │
```

## Top 5 Existential Risks

### 1. Fungal Fusion Is Not Fun (R12)

| Attribute | Detail |
|---|---|
| Probability | Low (prototyped concepts feel promising) |
| Impact | Critical (primary USP; if Fusion fails, the game's identity collapses) |
| Detection | Month 2 prototype, Month 6 playtest with 20 external testers |
| Mitigation | Weekly playtest iteration. Kill or redesign by Month 6 if unfun. |
| Contingency | Pivot to standard co-op (no fusion). Retain all other systems. Rename USP to Community Tree + Infection. |
| Cost of Contingency | 2-month schedule slip, $500K rework budget |

### 2. Rollback Netcode Instability at Scale (R01)

| Attribute | Detail |
|---|---|
| Probability | Medium |
| Impact | Critical (unplayable multiplayer = failed game) |
| Detection | Month 12 stress tests, Month 18 closed beta with 10K players |
| Mitigation | Custom deterministic ECS guarantees bitwise-identical simulation. Exhaustive desync detection. Server-side replay. |
| Contingency | Fall back to dedicated-server-authoritative model (no client prediction, higher latency but stable). Or license Fish-Net with custom rollback layer. |
| Cost of Contingency | 3-month delay, $800K engineering cost |

### 3. FixedPoint64 Math Bugs Causing Desyncs (R07)

| Attribute | Detail |
|---|---|
| Probability | Medium |
| Impact | Critical (desyncs break multiplayer entirely) |
| Detection | 256+ unit tests with golden reference table. Every CI run. Automated desync logging in playtests. |
| Mitigation | All math operations have Python decimal golden reference. Exhaustive edge case tests (overflow, underflow, division by near-zero). |
| Contingency | Emergency desync log system. Server-side replay for debugging. Hotfix turnaround < 24 hours. |

### 4. Scope Creep (R09)

| Attribute | Detail |
|---|---|
| Probability | High (30+ systems described in this bible) |
| Impact | High (delays, budget overrun, quality dilution) |
| Detection | Monthly scope review against MoSCoW list |
| Mitigation | Strict scope lock at Vertical Slice (Month 10). Features not in VS are COULD HAVE. |
| Contingency | Cut list (in priority order): Ecosystem sim, Rite of Echoes PvP, Wish Board, AI Director, Mycelium Hunters. Each cut saves 1-2 months. |

### 5. Market Timing Misfire

| Attribute | Detail |
|---|---|
| Probability | Medium |
| Impact | High (launching alongside a mega-hit kills visibility) |
| Detection | Monitor competitor announcements monthly starting Month 12 |
| Mitigation | Q1 2028 target avoids holiday congestion. Flexible launch window (can shift +/- 6 weeks). |
| Contingency | If major competitor announces same-month launch, delay 4-6 weeks. Marketing spend increase of $300K for counter-programming. |

## Risk Mitigation Budget

| Risk Category | Allocated Budget | % of Total |
|---|---|---|
| Technical risk (R01, R07) | $500,000 | 5% |
| Design risk (R03, R12) | $300,000 | 3% |
| Scope risk (R09) | $200,000 | 2% |
| Market risk (R05, R06) | $400,000 | 4% |
| Personnel risk (R10) | $200,000 | 2% |
| Infrastructure risk (R11) | $150,000 | 1.5% |
| Console risk (R04) | $100,000 | 1% |
| **Total Contingency** | **$1,850,000** | **18.5%** |

## Decision Points (Go/Kill Gates)

| Gate | Date | Decision | Kill Criteria |
|---|---|---|---|
| Fusion Prototype | Month 6 | Is Fusion fun? | NPS < 20 after 3 iterations |
| Vertical Slice | Month 10 | Is the core loop fun? | < 50% of testers want to play again |
| Netcode Stress Test | Month 14 | Can we handle 1000 CCU? | Desync rate > 5% under load |
| Closed Beta | Month 18 | Is the game shippable? | D7 retention < 20%, crash rate > 2% |
| Launch Decision | Month 22 | Ship or delay? | Any P0 bug, < 75% positive in open beta |

## Quarterly Risk Review Cadence

| Quarter | Focus | Attendees |
|---|---|---|
| Q1 (M1-3) | Technical feasibility: ECS, FixedPoint64, proc-gen | Tech Director, Producer |
| Q2 (M4-6) | Design validation: Fusion, Infection, combat feel | Lead Designer, Producer, QA Lead |
| Q3 (M7-9) | Integration risk: Netcode + Fusion + Community Tree | Full leads |
| Q4 (M10-12) | Scope assessment post-VS | Full leads + stakeholders |
| Q5 (M13-15) | Console certification readiness | Tech Director, Producer |
| Q6 (M16-18) | Beta performance: retention, stability, balance | Full team |
| Q7 (M19-21) | Launch readiness: marketing, infrastructure, content | Full team + marketing |
| Q8 (M22-24) | Final polish, launch window confirmation | Full team + stakeholders |
