# Rite of Echoes -- Scheduled PvP Mode

## Overview

The Rite of Echoes is a recurring competitive event that pits two teams against each other in a symmetrical arena. It is the only direct PvP content in the game.

## Schedule

| Parameter | Value |
|---|---|
| Frequency | Every 72 hours (real-time) |
| Registration window | Opens 2 hours before the Rite |
| Match duration | ~10 minutes (hard cap: 15 minutes) |
| Availability | 4-hour window per cycle (up to 24 matches per window) |
| Timezone handling | Three regional windows (NA, EU, APAC) staggered by 8 hours |

## Format

| Parameter | Value |
|---|---|
| Team size | 4v4 |
| Fusion allowed | Yes (up to 2 fused entities per team) |
| Loadout restrictions | None (bring your current build) |
| Infection carried in | Yes (affects Spore-Talk DSP but not combat stats) |
| Level scaling | Stats normalized to Zone 5 baseline. Boons active but capped at Tier 2. |

## Arena Design

- Symmetrical across central axis
- Procedurally generated from a pool of 12 arena templates
- Central feature: Spore Heart (capture deposit point)
- Each half contains:
  - 2 Echo fragment spawn points (fragments respawn 20s after capture)
  - 1 elevated sniper platform
  - 1 tunnel/flank route
  - 1 hazard zone (rotating, damages both teams)
- Arena dimensions: 80x80 units
- Walls and obstacles: destructible (respawn after 45s)

## Objective: Echo Fragment Capture

1. Echo fragments spawn at designated points (2 per side, 1 central contested)
2. Player picks up fragment by standing on spawn point for 3 seconds (interruptible)
3. Carrier is marked on both teams' HUDs (glowing outline visible through walls)
4. Carrier moves at 80% speed, cannot use dodge/dash
5. Deposit fragment at central Spore Heart (2-second channel, interruptible)
6. Successful deposit = 1 point for the team
7. First team to 7 points wins
8. If time expires: team with more points wins. If tied: sudden death (single fragment, first capture wins)

## Scoring

| Event | Points |
|---|---|
| Fragment deposit | 1 |
| Points to win | 7 |
| Sudden death trigger | Tied at 15-minute hard cap |

## Rewards

### Winners

| Reward | Value |
|---|---|
| Echo bonus | 500 Echo to chosen Community Tree branch |
| Rite Token | 1 (currency for Rite-exclusive cosmetics) |
| Title | "Rite Victor" (displayed for 72h until next Rite) |
| Infection purge | -5 Infection (minimum 0) |

### Losers

| Penalty/Reward | Value |
|---|---|
| Infection penalty | +10 Infection (unpurgeable for 24 hours) |
| Participation Echo | 100 Echo (consolation) |
| Consecutive loss: 3 | "Consumed" title (visible, removed on next win) |
| Consecutive loss: 5 | "Hollow" visual effect on player model for 72h |

## Spectator Mode

- Up to 20 spectators per match
- Elevated floating platforms around arena perimeter
- Free camera with lock-on-player option
- Mycelium Voice narrates key moments (dedicated Rite Narration line pool: 30 triggers, 4 variants each)
- Spectators can vote on "Most Valuable Spore" after match (cosmetic badge for winner)

## Combat Rules (Rite-Specific)

| Rule | Value |
|---|---|
| Respawn timer | 8 seconds |
| Respawn location | Team base (rear of arena half) |
| Death drop | Carried fragment drops at death location |
| Friendly fire | Disabled |
| Healing | Reduced to 50% effectiveness |
| Infection gain from PvP kills | 0 (Infection only from the Rite penalty) |

## Anti-Griefing

- AFK detection: no input for 30s triggers warning; 60s triggers kick + 72h Rite ban
- Intentional feeding detection: 5+ deaths in 2 minutes with no damage dealt triggers review flag
- Leaving penalty: treated as loss + additional +5 Infection
- Queue dodge (leaving during LOADING): 1-hour queue ban

## Matchmaking for Rite

- Separate Elo rating for Rite (not shared with PvE progression)
- Initial Elo: 1000
- K-factor: 32
- Team Elo = mean of team member Elos
- Match within 200 Elo range; expands by 50 every 30s of queue time
- Pre-made teams matched against pre-made teams when possible (2-minute grace before mixing)

## Implementation Notes

- Rite sessions use dedicated high-priority server instances (not shared with PvE)
- Arena generation seed broadcast at LOADING; clients build identical geometry
- Fragment state replicated as `NetworkVariable<FragmentState>` (spawned, carried, depositing)
- Spore Heart deposit channel uses server-authoritative timer (no client prediction)
- Rite schedule managed by a global Redis key `rite:next_start` with pub/sub announcement
