# Risk Tracking

## Risk Assessment Matrix

| Probability | Impact: Low | Impact: Medium | Impact: High | Impact: Critical |
|------------|------------|---------------|-------------|-----------------|
| High | Medium | High | Critical | Critical |
| Medium | Low | Medium | High | Critical |
| Low | Low | Low | Medium | High |
| Very Low | Negligible | Low | Low | Medium |

## Active Risk Register

### Technical Risks

| ID | Risk | Probability | Impact | Score | Mitigation | Owner |
|----|------|------------|--------|-------|-----------|-------|
| T01 | Rollback netcode has persistent desync issues | Medium | Critical | Critical | Comprehensive determinism tests, CI enforcement | Tech Director |
| T02 | FixedPoint64 precision insufficient for edge cases | Low | High | Medium | 256+ golden reference tests, Python decimal cross-check | Tech Director |
| T03 | Unity 6 upgrade breaks ECS bridge | Medium | Medium | Medium | Pin Unity version, test upgrades in branch first | Unity Programmer |
| T04 | Rust FFI crashes on console platforms | Medium | High | High | Test on dev kits from M5, fallback: C# proc-gen | Rust Developer |
| T05 | FMOD performance on Switch 2 exceeds budget | Medium | Medium | Medium | Profile early on Switch hardware, reduce bank sizes | Audio Director |
| T06 | Cross-platform determinism failure (ARM vs x86) | Low | Critical | Medium | Cross-platform CI tests, soft-float consideration | Tech Director |
| T07 | Community Tree backend scaling under load | Medium | High | High | Load test at M17, Supabase edge functions | Backend Developer |
| T08 | IL2CPP code generation issues on console | Low | High | Medium | Test IL2CPP builds weekly from M11 | Unity Programmer |

### Production Risks

| ID | Risk | Probability | Impact | Score | Mitigation | Owner |
|----|------|------------|--------|-------|-----------|-------|
| P01 | Key team member leaves during production | Medium | High | High | Knowledge sharing, documentation (bible), bus factor > 1 | Producer |
| P02 | Scope creep extends timeline past 24 months | High | High | Critical | Strict sprint scope, producer enforces cuts | Producer |
| P03 | Budget overrun before launch | Medium | Critical | Critical | Monthly budget review, phase-gated spending | Producer |
| P04 | Console certification failure delays launch | Medium | High | High | Pre-cert testing from M19, cert support vendor | Producer |
| P05 | Localization delays miss launch window | Medium | Medium | Medium | Start loc at M17, parallel to development | Producer |
| P06 | Vendor (VO studio) delivers late | Medium | Medium | Medium | Contractual deadlines, backup studio identified | Producer |

### Market Risks

| ID | Risk | Probability | Impact | Score | Mitigation | Owner |
|----|------|------------|--------|-------|-----------|-------|
| M01 | Competing roguelike launches in same window | Medium | Medium | Medium | Flexible launch date, unique Fusion USP | Producer |
| M02 | Steam algorithm buries visibility | Medium | High | High | Wishlist campaign, streamer outreach, demo release | Marketing |
| M03 | Negative reception of Spore-Talk mechanic | Low | High | Medium | Opt-in design, playtest validation at M10 | Lead Designer |
| M04 | Multiplayer-required design limits audience | Medium | Medium | Medium | Solo play fully supported with bots | Lead Designer |

## Risk Response Strategies

| Strategy | When to Apply | Example |
|----------|-------------|---------|
| **Avoid** | High probability + high impact | Cut feature entirely |
| **Mitigate** | Medium risks | Add tests, spike early, add buffer |
| **Transfer** | Vendor-related risks | Contracts, backup vendors |
| **Accept** | Low probability + low impact | Document and monitor |
| **Escalate** | Beyond team's control | Raise to publisher/stakeholders |

## Risk Review Cadence

| Review | Frequency | Participants | Action |
|--------|-----------|-------------|--------|
| Risk standup check | Daily (if active risks flagging) | Leads | Status update |
| Sprint risk review | Every sprint planning | Full team | New risks identified |
| Monthly risk board | Monthly | Leads + Producer | Full register review |
| Milestone risk gate | Each milestone | All stakeholders | Go/no-go decision |

## Contingency Plans

### T01: Persistent Desync

```
If desync rate > 2% at Alpha (M16):
  Trigger: Switch from client-prediction to lockstep for affected systems
  Cost: +2 sprints (4 weeks)
  Impact: Higher input latency (acceptable for co-op PvE)
  Decision point: M14
```

### T04: Rust FFI Console Failure

```
If Rust native plugin fails on any console:
  Trigger: Rewrite proc-gen in C# (already prototyped)
  Cost: +3 sprints (6 weeks)
  Impact: Slower proc-gen, acceptable for load times
  Decision point: M8
```

### P02: Scope Creep

```
Cut list (in priority order, cut from bottom):
  1. Heat levels 6-10 (ship with 0-5, add 6-10 post-launch)
  2. Zone 8 (ship with 7 zones, Zone 8 in Season 1)
  3. Switch 2 port (delay to post-launch)
  4. Localization (ship with 5 languages, add 5 post-launch)
  5. Community Tree (simplify to leaderboard only)
```

### P03: Budget Overrun

```
If spending exceeds 110% of budget at any phase gate:
  Action 1: Reduce team by 2-3 positions
  Action 2: Delay console ports to post-launch
  Action 3: Seek additional funding (publisher deal)
  Decision point: Monthly budget review
```

## Risk Scorecard (Updated Monthly)

| Month | Total Risks | Critical | High | Medium | Low | Trend |
|-------|------------|----------|------|--------|-----|-------|
| M1 | 14 | 2 | 4 | 6 | 2 | New |
| M4 | 12 | 1 | 3 | 6 | 2 | Improving |
| M10 | 10 | 1 | 2 | 5 | 2 | Stable |
| M16 | 8 | 0 | 2 | 4 | 2 | Improving |
| M20 | 6 | 0 | 1 | 3 | 2 | Good |
| M24 | 4 | 0 | 0 | 2 | 2 | Launch ready |

## Escalation Path

| Severity | Escalation To | Response Time |
|----------|-------------|---------------|
| Low risk flagged | Sprint retro | Next sprint |
| Medium risk triggered | Producer + Tech Director | Within 48h |
| High risk triggered | All leads meeting | Within 24h |
| Critical risk triggered | Emergency all-hands | Same day |
