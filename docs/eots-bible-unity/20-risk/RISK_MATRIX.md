# Risk Matrix

## Critical Risks (Impact: High, Probability: Medium-High)

| # | Risk | Impact | Prob | Mitigation | Owner |
|---|------|--------|------|-----------|-------|
| 1 | **Rollback netcode desync** | Ship-blocking | High | FixedPoint64 determinism, checksum validation every 4 ticks, extensive cross-platform testing | TD |
| 2 | **Fusion feels bad** | Core differentiator fails | Medium | Prototype Fusion by Month 3, 100+ playtester hours before vertical slice, iterate on input split ratio | Lead Designer |
| 3 | **Community Tree too slow/fast** | Server progression broken | Medium | Tunable cost formula (server-side), analytics on contribution rates, hotfix capability | Backend |
| 4 | **Scope creep (8 zones too many)** | Schedule slip | High | Vertical slice with 2 zones first, remaining zones are content (same systems), strict phase gates | Producer |
| 5 | **Performance on Switch** | Platform certification fail | Medium | 30fps render target, custom ECS designed for low overhead, profile monthly from Month 6 | TD |

## High Risks (Impact: High, Probability: Low-Medium)

| # | Risk | Impact | Prob | Mitigation |
|---|------|--------|------|-----------|
| 6 | **Unity licensing changes** | Budget impact, community backlash | Low | Custom ECS has zero Unity deps in Core/ — could port to Godot/custom if needed |
| 7 | **Spore-Talk VOIP latency** | Comedy mechanic unusable | Medium | Separate voice from game netcode, Opus PLC, test on degraded networks |
| 8 | **Rust FFI crashes** | Proc-gen unreliable | Low | Extensive Rust test suite, validation pass, fallback to C# proc-gen if needed |
| 9 | **Competitor ships similar game** | Market position eroded | Low | No known competitor has Fusion + Infection + Community Tree — triple moat |
| 10 | **Key team member leaves** | Knowledge loss | Medium | Bible documents everything, CLAUDE.md enables AI-assisted onboarding |

## Medium Risks

| # | Risk | Impact | Prob | Mitigation |
|---|------|--------|------|-----------|
| 11 | FMOD licensing cost increase | Budget | Low | FMOD indie license is free <$500K revenue, budget for Pro |
| 12 | Voice acting quality | Narrative impact | Medium | Cast Mycelium Voice early, record 100 lines as test, iterate |
| 13 | Battle Pass fatigue | Revenue below target | Medium | All gameplay content free, BP is cosmetic-only, generous free track |
| 14 | Cheating/hacking | Multiplayer integrity | Medium | Server-authoritative, no client-trusted state, rate limiting |
| 15 | Console certification delays | Launch date slip | Medium | Start cert process at Month 18 (4-month buffer) |

## Risk Response Plan

| Probability × Impact | Response |
|---------------------|----------|
| Critical (High × High) | Immediate mitigation, weekly review, escalation path |
| High (High × Medium) | Active mitigation, monthly review |
| Medium (Medium × Medium) | Monitor, quarterly review |
| Low (Low × Low) | Accept, document, review at phase gates |
