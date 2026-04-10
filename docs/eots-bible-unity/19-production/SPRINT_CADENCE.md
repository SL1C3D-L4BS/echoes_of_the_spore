# Sprint Cadence

## Sprint Structure

2-week sprints with ceremonies aligned to team growth phases.

| Day | Activity | Duration | Participants |
|-----|----------|----------|-------------|
| Monday (Sprint Start) | Sprint Planning | 2 hours | Full team |
| Monday | Sprint Goal Review | 15 min | Leads + Producer |
| Daily | Standup | 15 min | Full team |
| Wednesday (mid-sprint) | Playtest Session | 1 hour | Full team |
| Friday (Week 1) | Tech Review | 1 hour | Engineering |
| Thursday (Week 2) | Sprint Review / Demo | 1 hour | Full team + stakeholders |
| Friday (Week 2) | Retrospective | 45 min | Full team |
| Friday (Week 2) | Backlog Grooming | 1 hour | Leads + Producer |

## Sprint Capacity

| Phase | Team Size | Story Points/Sprint | Velocity Target |
|-------|-----------|-------------------|----------------|
| Pre-Prod (M1-4) | 8 | 40-50 | Establishing baseline |
| Vertical Slice (M5-10) | 15 | 75-90 | Calibrated |
| Alpha (M11-16) | 20 | 100-120 | Stable |
| Beta (M17-20) | 25 | 80-100 | Bug fix focus, lower velocity |
| Polish (M21-22) | 25 | 60-80 | Polish tasks, smaller scope |
| Launch (M23-24) | 25+ | 40-60 | Critical fixes only |

## Story Point Scale

| Points | Effort | Example |
|--------|--------|---------|
| 1 | Trivial (< 2 hours) | Fix typo in UI text |
| 2 | Small (half day) | Add new SFX event |
| 3 | Medium (1 day) | Implement single ECS system |
| 5 | Large (2-3 days) | Full boss AI implementation |
| 8 | Very Large (1 week) | Rollback netcode integration |
| 13 | Epic (needs splitting) | Full zone art pipeline |

Rule: No single story > 8 points. Anything larger must be split.

## Sprint Planning Process

```
1. Review sprint goal (Producer)
2. Pull stories from prioritized backlog
3. Each story:
   a. Read acceptance criteria
   b. Assign point estimate (team consensus)
   c. Identify dependencies
   d. Assign owner
4. Verify total <= team capacity
5. Commit to sprint backlog
6. Identify risks and blockers
```

## Definition of Done

| Criterion | Required |
|-----------|----------|
| Code compiles (zero warnings) | Yes |
| Unit tests written and passing | Yes |
| Integration tests passing (if applicable) | Yes |
| Determinism tests passing | Yes (for Core/ changes) |
| Code reviewed by at least 1 peer | Yes |
| Bible documentation updated (if spec changed) | Yes |
| No P0/P1 bugs introduced | Yes |
| Performance within budget | Yes |
| Works on all target platforms (verified or asserted) | Yes |
| Demo-ready in sprint review | Yes |

## Sprint Metrics Tracked

| Metric | Target | Alert |
|--------|--------|-------|
| Velocity (story points completed) | Within 20% of average | 2 sprints below target |
| Sprint completion rate | > 85% of committed stories | < 70% |
| Bug escape rate | < 2 bugs per sprint | > 5 bugs |
| Unplanned work ratio | < 20% of sprint capacity | > 30% |
| Blocked stories | < 10% of sprint | > 20% |
| Carry-over stories | < 2 per sprint | > 3 |

## Standup Format (15 min max)

```
Each person (< 90 seconds):
  1. What I completed yesterday
  2. What I'm working on today
  3. Any blockers

After round-robin:
  - Address blockers (parking lot for longer discussions)
  - Quick priority adjustments
```

## Sprint Board (Jira / Linear)

| Column | Meaning | WIP Limit |
|--------|---------|-----------|
| Backlog | Groomed but not in sprint | Unlimited |
| To Do | Committed for this sprint | — |
| In Progress | Actively being worked on | 2 per person |
| In Review | Code review + QA | 3 total |
| Done | Meets Definition of Done | — |
| Blocked | Waiting on dependency | Should be 0 |

## Release Train

| Cadence | Artifact | Destination |
|---------|----------|-------------|
| Every sprint (2 weeks) | Internal build | Team playtesting |
| Monthly | QA build | QA team deep testing |
| Milestone | External build | Stakeholders / publishers |
| Beta | Public build | Beta testers (Steam) |
| Launch | Gold master | All platforms |

## Emergency Hotfix Process

```
1. P0 bug reported
2. Producer creates hotfix branch from main
3. Fix developed and tested (< 24 hours)
4. Expedited code review (1 reviewer)
5. Merge to main + cherry-pick to develop
6. Emergency build + deploy
7. Post-mortem added to retro agenda
```

Does not affect sprint velocity or capacity.
