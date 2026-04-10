# Spore Plan -- Cooperative Turn-Based Phase

## Overview

Spore Plan is a cooperative tactical pause that rewards coordinated play. When activated, time slows dramatically and all players (or fusion roles) simultaneously queue actions from a shared budget. Actions then execute in rapid sequence.

## Activation Requirements

| Requirement | Value |
|---|---|
| Spore Gauge | Must be 100% full |
| Gauge fill source | Combat actions: damage dealt fills 1 point per mask of damage |
| Gauge capacity | 100 points |
| Fill rate (sustained combat) | ~60 seconds to fill from 0 |
| Passive fill | None. Gauge only fills from combat. |
| Gauge decay | -5 points/s if no combat for 10 seconds |
| Availability | All players in session (not per-player) |

## Time Slow Effect

| Parameter | Value |
|---|---|
| Sim rate during plan | 6 Hz (normal: 60 Hz) |
| Effective time scale | 10% (0.1x) |
| Real-time duration | 4 seconds |
| In-game elapsed time | 0.4 seconds |
| Visual effect | Blue-tint overlay, particle speed reduction, audio pitch drop to 0.3x |

## Planning Budget

Total budget: **100 points** (shared across all planners).

### Action Costs

| Action | Cost | Notes |
|---|---|---|
| Movement (per unit) | 1 point | Pathfinding preview shown |
| Light attack | 5 points | Single target |
| Heavy attack | 10 points | AoE or high-damage |
| Ability (by cooldown) | cooldown_seconds / 10 | e.g., 30s cooldown = 3 points |
| Dodge/dash | 3 points | Includes i-frames |
| Item use | 2 points | Consumables only |
| Spore-Talk ping | 0 points | Free communication |

### Budget Allocation (Fusion)

In fused state, budget is divided by role:

| Role | Budget Share |
|---|---|
| Pilot | 30 points (movement priority) |
| Gunner | 35 points (offense priority) |
| Support | 25 points (defense priority) |
| Relay | 10 points (utility) |

In non-fused 4-player sessions, each player gets 25 points.

## Planning Phase UX

1. Spore Gauge hits 100%. Any player can trigger with dedicated input.
2. Time slows. Blue overlay activates. Planning UI appears.
3. Each player/role sees their budget and available actions.
4. Players place waypoints, queue abilities, and set targets simultaneously.
5. Ghost previews show all planned paths (color-coded per player).
6. A shared countdown timer (4 real seconds) limits planning time.
7. Players can confirm early. If all confirm, execution begins immediately.

## Execution Phase

| Parameter | Value |
|---|---|
| Duration | 2 seconds real-time |
| Time scale during execution | 0.5x (partial slow) |
| Action ordering | Simultaneous by default; sequential if same target |
| Damage bonus | +20% for all queued actions (coordination reward) |
| Visual | Golden particle trails on all executing actions |

### Execution Priority (same target)

1. CC / crowd control abilities
2. Debuff abilities
3. Damage abilities (highest damage first)
4. Movement/positioning

## Recovery Phase

| Parameter | Value |
|---|---|
| Global cooldown | 5 seconds on all abilities post-execution |
| Spore Gauge | Reset to 0 |
| Immunity | 2-second damage immunity for all players after execution ends |

## Solo Variant: Micro-Plan

| Parameter | Spore Plan | Micro-Plan |
|---|---|---|
| Trigger | Full Spore Gauge (100) | Half Spore Gauge (50) |
| Time slow | 10% for 4s | 10% for 2s |
| Budget | 100 points | 50 points |
| Available actions | All | Movement + attack only (no abilities) |
| Execution time | 2s | 1s |
| Damage bonus | +20% | +10% |
| Recovery cooldown | 5s | 3s |

## Interaction with Fusion

- If triggered while fused, each role plans from their budget share
- Pilot plans movement for the fused entity
- Gunner plans attacks (aim targets shown as crosshair markers)
- Support places shield/heal zones
- Relay queues buffs (auto-targeted to fused entity)

## Interaction with Boss Fights

- Spore Plan is available during boss encounters
- Boss does NOT freeze during slow-time; boss actions continue at 10% speed
- Boss attack telegraphs remain visible and predictable at slow speed
- Some Phase 3 boss attacks interrupt Spore Plan activation (2s wind-up is vulnerable)

## Implementation Notes

- Planning input uses a separate `SporePlanInputState` that does not affect normal input processing
- Ghost preview rendering uses `LineRenderer` with dashed material per player color
- Budget is tracked in `SporeGaugeSystem` as an ECS singleton
- Execution queue serialized as `NativeList<PlannedAction>` sorted by priority
- Network: planning inputs sent as a single batch at confirmation, not per-tick
