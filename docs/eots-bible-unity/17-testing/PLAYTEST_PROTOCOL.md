# Playtest Protocol

## Playtest Types

| Type | Participants | Phase | Duration | Focus |
|------|-------------|-------|----------|-------|
| Desk Check | 1 developer | Ongoing | 15 min | Quick feature validation |
| Team Playtest | 4-8 team members | Weekly | 60 min | Cross-discipline feedback |
| Friends & Family | 20-50 external | Alpha | 2-4 hours | Fresh eyes, usability |
| Closed Beta | 10,000 players | Beta | 2 weeks | Scale, balance, bugs |
| Open Beta | 50,000+ players | Polish | 4 weeks | Final validation |
| Focus Group | 8-12 recruited | Any | 90 min | Specific feature deep-dive |

## Weekly Team Playtest

### Schedule

| Time | Activity |
|------|----------|
| 0:00 - 0:05 | Briefing: this week's focus area |
| 0:05 - 0:45 | Play session (2-3 full runs) |
| 0:45 - 1:00 | Debrief: structured feedback |

### Feedback Form (Per Session)

| Question | Response Type |
|----------|--------------|
| Overall fun (1-10) | Slider |
| Combat feel (1-10) | Slider |
| Difficulty (too easy / just right / too hard) | 3-point scale |
| Most fun moment | Free text |
| Most frustrating moment | Free text |
| Any bugs encountered? | Free text + screenshot |
| What would you change? | Free text |
| Fusion experience (1-10) | Slider |
| Spore-Talk hilarity (1-10) | Slider |

## Friends & Family Playtest

### Recruitment

| Criteria | Value |
|----------|-------|
| Count | 50 players minimum |
| Mix | 50% experienced roguelike players, 50% casual |
| Age | 18+ (avoid COPPA complications) |
| NDA | Required, signed before access |
| Compensation | Game key at launch + exclusive cosmetic |

### Session Structure

| Phase | Duration | Activity |
|-------|----------|----------|
| Onboarding | 10 min | Install, create account, tutorial |
| Free play | 90-120 min | Play naturally, no guidance |
| Guided tasks | 30 min | Specific scenarios (fuse, reach boss, etc.) |
| Survey | 15 min | Quantitative + qualitative feedback |
| Interview (subset) | 20 min | 1-on-1 with 10 selected participants |

### Data Collection

| Source | Data |
|--------|------|
| Telemetry | All gameplay events (see ANALYTICS_PIPELINE.md) |
| Screen recording | OBS capture of all sessions |
| Voice recording | Discord call during co-op sessions |
| Survey responses | Google Forms / Typeform |
| Bug reports | In-game reporter + Discord channel |
| Session replay | .eotsr replay files auto-saved |

## Closed Beta Protocol

### Entry Criteria

| Requirement | Threshold |
|------------|-----------|
| All 8 zones playable | 100% |
| All 4 classes implemented | 100% |
| Fusion fully functional | 100% |
| Spore-Talk operational | 100% |
| Community Tree live | 100% |
| Crash rate (internal) | < 1% |
| Desync rate (internal) | < 2% |
| Heat levels 0-10 | All functional |

### Metrics Tracked

| Metric | Target | Action if Missed |
|--------|--------|-----------------|
| DAU retention (D1) | > 35% | Review onboarding |
| Session length avg | > 20 min | Review pacing |
| Boss kill rate (Zone 1) | 40-70% | Adjust difficulty |
| Crash rate | < 0.5% | P0 hotfix |
| Desync rate | < 1% | Netcode investigation |
| Matchmaking time P95 | < 30s | Scale servers |
| Fusion usage rate | > 30% of sessions | Review incentives |
| Spore-Talk opt-in | > 50% | Review UX |
| NPS score | > 30 | Review overall experience |

### Bug Triage During Beta

| Severity | Response Time | Fix Window | Examples |
|----------|-------------|-----------|----------|
| P0 (blocker) | < 2 hours | < 24 hours | Crash, data loss, exploit |
| P1 (critical) | < 8 hours | < 72 hours | Desync, major balance, soft-lock |
| P2 (major) | < 24 hours | Next patch | Visual glitch, minor balance |
| P3 (minor) | < 48 hours | Backlog | Typo, cosmetic, polish |

## Playtest Environment

| Component | Setup |
|-----------|-------|
| Build distribution | Steam beta branch (closed) |
| Server environment | Staging cluster (mirrors production) |
| Telemetry endpoint | Staging analytics pipeline |
| Communication | Private Discord server |
| Bug reporting | In-game F8 reporter → Jira |
| Build updates | 2x per week during beta |

## Exit Criteria (Beta → Polish)

| Metric | Required |
|--------|----------|
| NPS score | > 40 |
| Crash rate | < 0.3% |
| Desync rate | < 0.5% |
| D1 retention | > 40% |
| D7 retention | > 20% |
| All P0/P1 bugs resolved | 100% |
| Player sentiment (surveys) | > 70% positive |
| Community Tree functional at scale | Verified |
| All zones cleared by at least 10 players | Verified |
