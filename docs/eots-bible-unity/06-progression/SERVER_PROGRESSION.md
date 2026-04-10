# Server Progression (Community Skill Tree)

## Overview

Each server hosts a shared Community Skill Tree with 5 branches x 20 depths = 100 nodes. All players on a server contribute Echoes collectively. Server progression defines the metagame arc from launch through endgame, including catch-up mechanics for players who join late.

## Server Lifecycle Timeline

| Phase | Timeframe | Tree State | Player Activity |
|-------|-----------|------------|----------------|
| Launch | Day 1-7 | Depth 1-5 across branches | Exploration, learning, high contribution |
| Early Growth | Week 2-4 | Depth 5-10 | Core loop mastered, community forming |
| Mid Season | Month 2-3 | Depth 10-15 | Steady progression, branch voting matters |
| Late Season | Month 4-6 | Depth 15-20 | Capstone pushes, endgame content |
| Endgame | Month 6+ | All Depth 20 (eventually) | True Ending vote, server identity cemented |

## Cost Formula and Projections

```
cost(depth) = floor(1000 x 1.8^depth)
```

### Full Cost Table (Per Branch)

| Depth | Node Cost | Cumulative (Branch) | Server Pop Needed (Est.) |
|-------|-----------|--------------------|-----------------------|
| 1 | 1,800 | 1,800 | 10 players, Day 1 |
| 2 | 3,240 | 5,040 | 10 players, Day 2 |
| 3 | 5,832 | 10,872 | 10 players, Day 3 |
| 5 | 18,896 | 48,498 | 25 players, Week 1 |
| 8 | 110,199 | 372,709 | 50 players, Week 3 |
| 10 | 357,047 | 1,233,569 | 100 players, Month 1 |
| 13 | 2,082,265 | 6,428,230 | 200 players, Month 2 |
| 15 | 6,746,191 | 20,834,573 | 300 players, Month 3 |
| 18 | 39,346,919 | 119,597,321 | 500 players, Month 5 |
| 20 | 127,482,280 | 388,006,790 | 500+ players, Month 6+ |

### Total Echoes for All 5 Branches

| Milestone | Total Echoes Required |
|-----------|----------------------|
| All branches Depth 5 | 242,490 |
| All branches Depth 10 | 6,167,845 |
| All branches Depth 15 | 104,172,865 |
| All branches Depth 20 | 1,940,033,950 |

## Contribution System

| Parameter | Value |
|-----------|-------|
| Echo earn rate (base, Heat 0) | 100-500 per run |
| Heat multiplier | `1.0 + (heat x 0.05)` |
| Average run duration | 30-60 minutes |
| Post-run branch selection | Player chooses 1 of 5 branches |
| Spore Pass multiplier | 2x contribution (does NOT gate content) |
| Maximum contribution per run | No cap |

### Contribution Leaderboard

| Metric | Tracked | Display |
|--------|---------|---------|
| Total individual contribution | Per player | Server leaderboard |
| Contribution this week | Per player | Weekly board |
| Branch-specific contribution | Per player per branch | Branch detail view |
| Server total | Aggregate | Canopy tree visual growth |

## Branch Voting System

| Parameter | Value |
|-----------|-------|
| Eligible voters | Spore Pass holders |
| Vote frequency | Weekly (Monday 00:00 UTC reset) |
| Vote weight | 1 vote per player (equal weight) |
| Priority branch effect | Selected branch receives 1.5x all contributions for that week |
| Tie resolution | Alphabetical branch order |
| Minimum voters | 10 (below threshold: no priority branch that week) |

## Catch-Up Mechanics for Late Joiners

Late joiners face a significant Echo deficit. These mechanics ensure they can contribute meaningfully without trivializing the effort of early players.

| Mechanism | Effect | Trigger |
|-----------|--------|---------|
| Echo Bonus (server age) | +1% Echo earn rate per server day (max +100%) | Automatic |
| Newcomer Boost | 3x contribution for first 7 days on server | Joining a server with avg Depth 5+ |
| Depth Discount | Nodes below server-average depth cost 50% | When player contributes to below-avg branch |
| Catch-Up Boon | Free Rare boon per run for first 20 runs | New players only |

### Catch-Up Curve

| Server Age When Joining | Echo Bonus | Effective Earn Rate |
|-------------------------|-----------|-------------------|
| Week 1 (launch) | +0-7% | 1.0-1.07x |
| Month 1 | +30% | 1.30x |
| Month 3 | +90% | 1.90x |
| Month 6+ | +100% (cap) | 2.00x |

## Server Transfer

| Parameter | Value |
|-----------|-------|
| Transfer cost | Free (once per 30 days) |
| Cooldown | 30 days |
| What transfers | Account progression, cosmetics, stats |
| What does NOT transfer | Community Tree contribution history, leaderboard rank |
| Cross-server portal | Community Tree: Infrastructure Depth 20 (visit without transferring) |

## Server Merge Policy

| Trigger | Action |
|---------|--------|
| Server population < 50 active/week for 30 days | Merge candidate |
| Merge behavior | Higher-progress tree preserved; lower-tree contributions added |
| Player notification | 7-day notice with opt-out transfer window |
| Leaderboard | Merged, ranked by total contribution |

## Physical Tree Visualization (Canopy)

| Depth Range | Tree Appearance |
|-------------|----------------|
| 0-5 | Sapling with 1-5 small branches |
| 6-10 | Young tree, leaves appearing, glowing nodes |
| 11-15 | Mature tree, bioluminescent bark, wildlife |
| 16-19 | Ancient tree, canopy covers hub area, ambient music shifts |
| 20 (all branches) | World Tree: transforms entire Canopy, unique skybox |

## Backend Architecture

```
Supabase: skill_tree_nodes table
  - server_id (uuid)
  - branch (enum: expansion/evolution/ecology/infrastructure/lore)
  - depth (int 1-20)
  - current_echoes (bigint)
  - required_echoes (bigint)
  - unlocked (boolean)
  - unlocked_at (timestamp)

Redis: real-time cache
  - tree:{server_id}:{branch}:current -> current contribution
  - tree:{server_id}:{branch}:depth -> current depth
  - tree:{server_id}:votes:{branch} -> weekly vote count
  - Flush to Supabase every 60 seconds

Events:
  - on_depth_unlock -> broadcast to all connected clients
  - on_depth_unlock -> trigger server-wide notification
  - on_depth_20 -> trigger cinematic event for all online players
```
