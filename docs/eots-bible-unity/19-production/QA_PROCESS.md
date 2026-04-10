# QA Process

## QA Team Structure

| Role | Count | Phase Active | Responsibility |
|------|-------|-------------|---------------|
| QA Lead | 1 | M5+ (Vertical Slice) | Process, test plans, triage |
| Internal QA | 2 | M11+ (Alpha) | Daily testing, regression |
| Console QA | 1 | M17+ (Beta) | Platform-specific testing |
| External QA | 8-12 | M18-M22 (Beta/Polish) | Outsourced functional QA |
| Community QA | 100+ | M18+ (Beta) | Beta tester bug reports |

## Bug Lifecycle

```
[New] → [Triaged] → [Assigned] → [In Progress] → [Fixed] → [Verified] → [Closed]
                  ↘ [Won't Fix] → [Closed]
                  ↘ [Duplicate] → [Closed]
                  ↗ [Reopened] ←─────────────────── (if fix fails verification)
```

## Bug Severity Definitions

| Severity | Definition | Examples | SLA |
|----------|-----------|---------|-----|
| P0 (Blocker) | Game-breaking, data loss, security | Crash, save corruption, exploit | Fix within 24h |
| P1 (Critical) | Major feature broken, workaround exists | Desync, soft-lock, balance-breaking | Fix within 72h |
| P2 (Major) | Feature impaired, minor impact | Visual glitch, audio pop, UI overlap | Fix in next sprint |
| P3 (Minor) | Cosmetic, polish | Typo, minor animation, color off | Backlog |
| P4 (Trivial) | Suggestion, nice-to-have | UX improvement, tooltip wording | Backlog |

## Bug Report Template

```
Title: [System] Brief description
Severity: P0/P1/P2/P3/P4
Platform: PC/PS5/Xbox/Switch/All
Build: v0.4.2-build-1234
Frequency: Always / Often / Sometimes / Rare / Once

Steps to Reproduce:
1. Start a new run with Bind class
2. Enter Zone 3, Room 5
3. Attack Mycelium Hunter while infected at tier 50+
4. Observe crash

Expected Result: Game continues normally
Actual Result: Crash to desktop with NullReferenceException

Attachments:
- Screenshot/video
- Player.log (crash log)
- Replay file (.eotsr)
- System specs (if relevant)
```

## Test Plan Structure

### Smoke Test (Per Build, 30 min)

| Test | Steps | Pass Criteria |
|------|-------|--------------|
| Boot | Launch game | Menu appears < 5s |
| New Run | Start run, Zone 1 | Loads, player spawns |
| Combat | Kill 5 enemies | Damage, death, loot |
| Boss | Reach and fight Zone 1 boss | Boss spawns, AI works |
| Fusion | Fuse with partner | Merge, play, separate |
| VOIP | Speak in co-op | Voice heard by partner |
| Save | Quit and resume | State preserved |
| Settings | Change all settings | Apply correctly |

### Full Regression (Per Sprint, 4 hours)

| Area | Tests | Duration |
|------|-------|----------|
| All 8 zones (walkthrough) | Enter, fight, boss, clear | 120 min |
| All 4 classes | Basic moveset per class | 30 min |
| All 12 fusion pairs | Merge, fight, separate | 30 min |
| Infection (all tiers) | Reach 100%, verify DSP + buffs | 15 min |
| Boon system | Select, apply, stack | 15 min |
| Community Tree | Deposit echoes, verify stage | 10 min |
| Settings | All video, audio, control settings | 10 min |
| Localization (spot check) | 3 languages, key screens | 15 min |
| Network | 4-player co-op session | 15 min |
| Heat system | Heat 0, 5, 10 | 15 min |
| Edge cases | Controller disconnect, network drop | 15 min |

### Platform-Specific Test (Per Platform, 2 hours)

| Test | PS5 | Xbox | Switch |
|------|-----|------|--------|
| Boot + splash screens | Order, timing | Order, timing | Order, timing |
| Suspend/resume (x10) | PS button | Quick Resume | Sleep button |
| Controller disconnect | DualSense | Xbox controller | Joy-Con/Pro |
| Network disconnect | Pull cable | Pull cable | Disable WiFi |
| Trophies/achievements | 3 sample trophies | 3 sample achievements | N/A |
| Resolution modes | 4K output | 4K + 1080p (Series S) | Docked + handheld |
| Save data | Create, load, delete | Create, load, delete | Create, load, delete |
| System overlay | PS menu, party, share | Xbox guide, party | Home menu |

## Automated QA

| Test Type | Count | Run Frequency | Duration |
|-----------|-------|-------------|----------|
| Unit tests | 575+ | Every CI push | < 40s |
| Integration tests | 20+ | Every CI push | < 7 min |
| Determinism tests | 5 | Every CI push | < 15s |
| Regression tests | 100+ | Every CI push | < 30s |
| Load tests | 7 scenarios | Weekly-monthly | 5-60 min |
| Balance simulations | 6 types | Nightly | 30 min |
| Screenshot comparison | All UI screens | Nightly | 5 min |

## Bug Metrics Dashboard

| Metric | Target | Alert |
|--------|--------|-------|
| Open P0 count | 0 | Any P0 open > 24h |
| Open P1 count | < 5 | > 10 |
| Total open bugs | < 50 | > 100 |
| Bug inflow rate | < 15/week (beta) | > 25/week |
| Bug close rate | > inflow rate | Close < inflow for 2 weeks |
| Mean time to fix (P0) | < 24h | > 48h |
| Mean time to fix (P1) | < 72h | > 1 week |
| Verification backlog | < 10 | > 20 |
| Regression rate | < 5% | > 10% |

## QA Environment

| Environment | Purpose | Build Source |
|------------|---------|-------------|
| Dev (local) | Developer testing | Unity Editor |
| QA (internal) | Formal QA testing | CI build (develop branch) |
| Staging | Pre-release testing | CI build (main branch) |
| Beta | External tester access | Steam beta branch |
| Production | Live players | Steam default branch |

## In-Game Bug Reporter

| Feature | Specification |
|---------|--------------|
| Hotkey | F8 (rebindable) |
| Captures | Screenshot, player.log tail, replay last 30s |
| Form | Severity dropdown, description text field |
| Submission | HTTPS POST to bug tracking API |
| Destination | Auto-creates Jira/Linear ticket |
| Feedback | "Bug reported, thanks!" toast notification |
| Rate limit | 1 report per 60 seconds |
