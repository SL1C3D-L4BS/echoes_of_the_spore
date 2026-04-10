# Post-Mortem Template

## When to Conduct

| Trigger | Type | Duration |
|---------|------|----------|
| Major milestone completed | Milestone post-mortem | 90 min |
| Critical incident (P0 outage) | Incident post-mortem | 60 min |
| Sprint with < 70% completion | Sprint post-mortem | 45 min |
| Project completion (launch) | Full project post-mortem | 3 hours |
| Season/DLC completion | Content post-mortem | 90 min |

## Post-Mortem Meeting Structure

| Phase | Duration | Activity |
|-------|----------|----------|
| 1. Context Setting | 10 min | Review milestone goals, timeline, team |
| 2. Data Review | 15 min | Metrics, velocity, bug counts, KPIs |
| 3. What Went Well | 20 min | Individual input, then group discussion |
| 4. What Went Wrong | 20 min | Individual input, then group discussion |
| 5. Root Cause Analysis | 15 min | Identify systemic causes, not blame |
| 6. Action Items | 10 min | Concrete, assigned, time-bound |
| 7. Wrap Up | 5 min | Summarize, schedule follow-up |

## Document Template

```
# [Milestone/Incident Name] Post-Mortem
Date: YYYY-MM-DD
Facilitator: [Name]
Participants: [List]

## Summary
One paragraph describing the milestone/incident and outcome.

## Timeline
| Date | Event |
|------|-------|
| YYYY-MM-DD | [Key event] |
| YYYY-MM-DD | [Key event] |

## Metrics
| Metric | Target | Actual | Delta |
|--------|--------|--------|-------|
| Sprint velocity | X pts | Y pts | +/-Z |
| Bug count (open) | X | Y | +/-Z |
| Crash rate | X% | Y% | +/-Z |
| Schedule adherence | On time | +/- N days | |

## What Went Well
1. [Item with supporting evidence]
2. [Item with supporting evidence]
3. [Item with supporting evidence]

## What Went Wrong
1. [Item with root cause analysis]
2. [Item with root cause analysis]
3. [Item with root cause analysis]

## Root Causes
| Issue | Root Cause | Category |
|-------|-----------|----------|
| [Issue] | [Why it happened] | Process / Technical / People / External |

## Action Items
| Action | Owner | Due Date | Status |
|--------|-------|----------|--------|
| [Specific action] | [Name] | YYYY-MM-DD | Open |

## Lessons Learned
1. [Lesson that applies to future work]
2. [Lesson that applies to future work]
```

## Incident Post-Mortem Template

For P0 outages, server crashes, or critical live issues:

```
# Incident Post-Mortem: [Title]
Date: YYYY-MM-DD
Severity: P0
Duration: X hours Y minutes
Impact: [Number of affected players, sessions lost]

## Incident Timeline
| Time (UTC) | Event |
|-----------|-------|
| HH:MM | First alert triggered |
| HH:MM | On-call engineer notified |
| HH:MM | Root cause identified |
| HH:MM | Fix deployed |
| HH:MM | Service restored |
| HH:MM | All-clear confirmed |

## Root Cause
[Technical explanation of what went wrong]

## Detection
How was the issue detected?
  [ ] Automated monitoring alert
  [ ] Player report
  [ ] Internal team noticed
  [ ] Other: ___

Time to detect: X minutes
Time to respond: X minutes
Time to resolve: X minutes

## Impact
| Metric | Value |
|--------|-------|
| Players affected | N |
| Sessions disrupted | N |
| Data loss | Yes/No (details) |
| Revenue impact | $X (if applicable) |

## Fix Applied
[Description of the fix]

## Prevention
| Action | Owner | Due Date |
|--------|-------|----------|
| [Prevent recurrence] | [Name] | YYYY-MM-DD |
| [Improve detection] | [Name] | YYYY-MM-DD |
| [Improve response] | [Name] | YYYY-MM-DD |

## Five Whys
1. Why did the outage happen?
   → [Answer]
2. Why did [Answer 1] happen?
   → [Answer]
3. Why did [Answer 2] happen?
   → [Answer]
4. Why did [Answer 3] happen?
   → [Answer]
5. Why did [Answer 4] happen?
   → [Root cause]
```

## Post-Mortem Categories for Tracking

| Category | Description | Examples |
|----------|-------------|---------|
| Process | Workflow, communication, planning issues | Unclear requirements, missed handoff |
| Technical | Architecture, tooling, infrastructure | Bad design decision, tech debt |
| People | Hiring, skills, team dynamics | Key person unavailable, skill gap |
| External | Vendor, platform, market factors | Vendor delay, API change, competitor |
| Scope | Feature size, complexity estimates | Underestimated complexity |

## Action Item Tracking

All post-mortem action items are tracked in a central register:

| Post-Mortem | Date | Action Items | Completed | Completion Rate |
|------------|------|-------------|-----------|----------------|
| M4 Art Lock | — | 5 | 5 | 100% |
| M10 VS | — | 8 | 7 | 87% |
| M16 Alpha | — | 6 | — | — |
| Incident: Server crash | — | 3 | — | — |

Target: > 90% of action items completed within 2 sprints.

## Blameless Culture Rules

| Rule | Enforcement |
|------|------------|
| No naming individuals in "what went wrong" | Facilitator moderates |
| Focus on systems and processes, not people | Facilitator redirects |
| Everyone contributes (anonymous input allowed) | Use sticky notes or forms |
| Action items are systemic, not personal | Review before committing |
| Post-mortems are mandatory, not punitive | Producer schedules |
| Post-mortem documents are shared with full team | Published to shared drive |

## Archive

All post-mortem documents stored in:
`docs/eots-bible-unity/19-production/post-mortems/`

Naming: `YYYY-MM-DD_[type]_[title].md`

Examples:
- `2026-10-15_milestone_vertical-slice.md`
- `2027-01-20_incident_server-crash.md`
- `2027-06-01_project_launch.md`
