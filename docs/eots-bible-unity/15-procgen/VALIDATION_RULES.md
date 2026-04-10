# Validation Rules

## Overview

Validation runs as Phase 5 of zone graph generation. Every generated zone must pass all validation checks before being accepted. Failed validation triggers a retry with an incremented seed. The target retry rate is below 5% (fewer than 5 in 100 seeds should require retry).

## Validation Check Inventory

| Check | Category | Severity | Action on Fail |
|-------|----------|----------|---------------|
| Connectivity | Structure | Fatal | Retry |
| Critical path length | Structure | Fatal | Retry |
| Boss reachability | Structure | Fatal | Retry |
| Rest before boss | Pacing | Fixable | Insert Rest room |
| Room type variety | Pacing | Fixable | Swap room type |
| Difficulty curve | Balance | Fixable | Re-sort difficulties |
| Room count range | Structure | Fixable | Add/remove rooms |
| Secret placement | Content | Fixable | Reattach or remove |
| Door connectivity | Geometry | Fatal | Retry |
| Minimum path length | Structure | Fatal | Retry |

## Connectivity Check

### Algorithm

Breadth-first search from entrance node (room 0). All room nodes must be reachable.

```
1. Start BFS from room 0 (entrance).
2. Mark each visited room.
3. After BFS completes, check: visited_count == total_room_count.
4. If any room unreachable: FAIL.
```

### Edge Cases

| Scenario | Handling |
|----------|----------|
| Secret rooms | Must be reachable via at least one Secret edge |
| Shortcut-only rooms | Must also be reachable via non-Shortcut path |
| Bidirectional loops | Both directions checked in BFS |
| Isolated subgraphs | Fatal failure, full retry |

## Difficulty Curve Validation

### Monotonic Increase on Critical Path

| Rule | Detail |
|------|--------|
| Direction | Difficulty must increase or stay equal along critical path |
| Tolerance | A single dip of up to 10 difficulty points is allowed (rest rooms) |
| Recovery | After a dip, next room must exceed the pre-dip difficulty |
| Boss difficulty | Always the highest value in the zone (100 for final zone) |

### Difficulty Value Ranges

| Zone | Entry Difficulty | Mid Difficulty | Boss Difficulty |
|------|-----------------|----------------|-----------------|
| 1 | 5-10 | 15-25 | 30 |
| 2 | 15-20 | 25-35 | 45 |
| 3 | 25-30 | 35-45 | 55 |
| 4 | 35-40 | 45-55 | 65 |
| 5 | 40-45 | 50-60 | 70 |
| 6 | 50-55 | 60-70 | 80 |
| 7 | 55-60 | 65-80 | 90 |
| 8 | 65-70 | 75-90 | 100 |

### Fix Strategy

If difficulty curve is non-monotonic:
1. Collect all critical path room difficulties.
2. Sort ascending.
3. Reassign sorted values to critical path rooms in order.
4. Preserve room type assignments (only difficulty values change).

## Minimum Path Length

### Rules

| Rule | Value |
|------|-------|
| Minimum critical path | 6 rooms (entrance to boss, inclusive) |
| Minimum total path options | At least 2 distinct routes from entrance to boss |
| Maximum critical path | 12 rooms |
| Minimum branching | At least 2 non-critical-path rooms |

### Failure Conditions

| Condition | Action |
|-----------|--------|
| Critical path < 6 rooms | Retry (seed cannot produce viable layout) |
| Critical path > 12 rooms | Retry |
| Zero branches | Retry |
| Single route to boss (no alternatives) | Acceptable if room count target < 18 |

## Boss Placement Rules

### Requirements

| Rule | Detail |
|------|--------|
| Position | Terminal node on critical path (no rooms after boss) |
| Predecessor | At least 1 non-combat room within 2 edges of boss (rest or reward) |
| Entry | Exactly 1 incoming edge (boss gate) |
| Exit | Exactly 1 outgoing edge (to reward room and zone transition) |
| No shortcuts to boss | No Shortcut edges connecting directly to boss room |
| No secrets behind boss | No Secret rooms accessible only through the boss room |

### Fix Strategy

If rest-before-boss check fails:
1. Find the room 2 edges before boss on critical path.
2. Change its type to Rest (preserve other properties).
3. Re-validate.

## Room Type Distribution Rules

### Consecutive Type Limit

| Rule | Detail |
|------|--------|
| Max consecutive same type | 3 on critical path |
| Exception | Combat rooms may appear up to 3 times consecutively |
| Fix | Swap middle room to next most-probable type |

### Required Types Per Zone

| Room Type | Minimum Count | Maximum Count |
|-----------|--------------|--------------|
| Combat | 40% of rooms | 70% of rooms |
| Puzzle | 1 per zone | 4 per zone |
| Reward | 2 per zone | 5 per zone |
| Rest | 1 per zone | 3 per zone |
| Boss | Exactly 1 | Exactly 1 |
| Transition | 1 (entrance) + 1 (exit) | 3 (if multi-entrance zone) |
| Secret | 1 per zone | 3 per zone |

## Retry Rate Monitoring

### Target Metrics

| Metric | Target | Alert Threshold |
|--------|--------|----------------|
| Retry rate (overall) | < 5% | > 8% |
| Retry rate (per zone) | < 10% | > 15% |
| Max retries per generation | 10 | N/A (hard limit) |
| Hard failures (10 retries exceeded) | < 0.01% | > 0.1% |

### Monitoring Pipeline

```
Generation call -> [success/retry/failure] -> Telemetry event
  -> Aggregated per zone_id, per seed range
  -> Dashboard alert if threshold exceeded
  -> If alert: investigate zone config, adjust weights
```

### Historical Retry Rates (Benchmark)

| Zone | Seeds Tested | Retry Rate | Avg Retries When Needed |
|------|-------------|-----------|------------------------|
| 1 | 10,000 | 2.1% | 1.3 |
| 2 | 10,000 | 3.4% | 1.5 |
| 3 | 10,000 | 2.8% | 1.4 |
| 4 | 10,000 | 4.2% | 1.7 |
| 5 | 10,000 | 3.1% | 1.4 |
| 6 | 10,000 | 3.9% | 1.6 |
| 7 | 10,000 | 4.5% | 1.8 |
| 8 | 10,000 | 4.8% | 2.1 |

Zone 8 has the highest retry rate due to larger room count targets and stricter boss placement rules.

## Validation Error Codes

| Code | Error | Severity |
|------|-------|----------|
| V001 | Disconnected room(s) detected | Fatal |
| V002 | Critical path too short | Fatal |
| V003 | Critical path too long | Fatal |
| V004 | Boss room unreachable | Fatal |
| V005 | No rest room before boss | Fixable |
| V006 | Consecutive same-type rooms exceeded | Fixable |
| V007 | Difficulty curve non-monotonic | Fixable |
| V008 | Room count out of range | Fixable |
| V009 | Secret room orphaned | Fixable |
| V010 | Door connectivity mismatch | Fatal |
| V011 | Boss room has multiple entries | Fatal |
| V012 | Shortcut bypasses boss | Fatal |

## Implementation Notes

- Validation in `eots-procgen-zone/src/validation.rs`
- Returns `Vec<ValidationError>` with error code + affected room IDs
- Fixable errors handled by `auto_fix()` function before retry decision
- Fatal errors trigger immediate retry with `seed + 1`
- Retry counter tracked per `generate_zone()` call, hard cap at 10
- Telemetry: validation results included in FFI output metadata (optional debug field)
- Benchmark test: `cargo test --release benchmark_retry_rate` generates 10K zones per zone_id
