# Zone Graph Algorithm

## Overview

The zone graph algorithm generates a directed acyclic graph (DAG) of rooms for each zone. The algorithm runs in Rust (`eots-procgen-zone` crate) and produces a `ZoneGraphData` struct consumed by Unity. The algorithm operates in 5 sequential phases.

## Input Parameters

| Parameter | Type | Source |
|-----------|------|--------|
| zone_id | u8 (1-8) | Server / delve config |
| seed | u64 | Derived from server seed (see SEED_SYSTEM.md) |
| room_count_target | u8 (15-30) | Zone config table |
| party_size | u8 (1-4) | Matchmaking result |
| heat_level | u8 (0-10) | Player-selected difficulty |

### Room Count Per Zone

| Zone | Min Rooms | Max Rooms | Target |
|------|-----------|-----------|--------|
| 1 Sporehaven | 15 | 20 | 18 |
| 2 Cortex | 18 | 22 | 20 |
| 3 Ferment Pits | 16 | 22 | 19 |
| 4 Chitinhall | 20 | 25 | 22 |
| 5 Rot Garden | 18 | 22 | 20 |
| 6 Myco-Forge | 18 | 24 | 21 |
| 7 Necroveil | 20 | 25 | 22 |
| 8 Core | 22 | 30 | 25 |

## Phase 1: Critical Path

Generate the minimum path from entrance to boss room.

### Algorithm

1. Create entrance node (room 0, type = Transition).
2. Generate a chain of rooms: length = ceil(room_count_target * 0.4).
3. Room types selected by weighted random using zone weight table (see PROCGEN.md).
4. Assign increasing difficulty values along the chain (linear ramp).
5. Final node is always Boss type.
6. Connect sequentially: room[i] -> room[i+1].

### Critical Path Constraints

| Constraint | Value |
|-----------|-------|
| Min length | 6 rooms |
| Max length | 12 rooms |
| Must contain | At least 1 Rest room before Boss |
| Must contain | At least 1 Reward room |
| No consecutive | No two rooms of the same type adjacent |
| Boss position | Always terminal node on critical path |

### Edge Weights

Each edge in the graph has a weight representing traversal cost:

| Edge Type | Weight | Meaning |
|-----------|--------|---------|
| Standard | 1 | Normal connection |
| Difficult | 2 | Requires defeating room enemies to pass |
| Shortcut | 0 | Community Tree unlock, bypasses rooms |
| Secret | 1 | Hidden, only discoverable via Insight or exploration |

## Phase 2: Branching

Add branch paths off the critical path to reach target room count.

### Algorithm

1. Remaining rooms = room_count_target - critical_path_length.
2. Select branch points on critical path (every 2-3 rooms, randomized).
3. For each branch point, generate a sub-chain of 1-4 rooms.
4. Branch type distribution: 60% optional combat, 30% reward/rest, 10% puzzle.
5. Connect branch root to critical path node.
6. Dead-end branches terminate with reward or secret rooms.

### Branch Constraints

| Constraint | Value |
|-----------|-------|
| Max branch depth | 4 rooms from critical path |
| Min branches | 2 per zone |
| Max branches | 6 per zone |
| Branch-to-branch | Branches cannot spawn sub-branches (depth 1 only) |

## Phase 3: Loops

Add loop connections to create non-linear exploration.

### Algorithm

1. Identify pairs of non-adjacent rooms on different branches or at different critical path positions.
2. For each candidate pair, check: would connecting them create a cycle of length 4-8?
3. If yes, add a loop edge with 30% probability (seeded).
4. Loop edges are always standard weight (not shortcuts or secrets).
5. Maximum 2 loops per zone graph.

### Loop Constraints

| Constraint | Value |
|-----------|-------|
| Min cycle length | 4 rooms |
| Max cycle length | 8 rooms |
| Max loops | 2 per zone |
| No boss bypass | Loops cannot create a path that skips the boss room |
| Directionality | Loop edges are bidirectional (player can traverse either way) |

## Phase 4: Secrets

Place secret rooms and hidden connections.

### Algorithm

1. Target: 1-3 secret rooms per zone (based on zone_id; later zones have more).
2. Select attachment points on existing graph (prefer dead-end rooms and mid-branch nodes).
3. Create Secret room node connected via Secret edge (hidden connection).
4. Secret rooms contain: rare loot, Fossil Murals, or unique encounters.
5. Secret edges are invisible on the minimap until discovered.

### Secret Room Distribution

| Zone | Secret Rooms | Content Focus |
|------|-------------|---------------|
| 1-2 | 1 | Fossil Mural |
| 3-4 | 2 | Mural + rare loot |
| 5-6 | 2 | Mural + unique encounter |
| 7-8 | 3 | Mural + rare loot + lore cache |

## Phase 5: Validation

Verify the generated graph meets all quality constraints.

### Validation Checks

| Check | Criteria | Action on Fail |
|-------|----------|---------------|
| Connectivity | All rooms reachable from entrance | Retry generation |
| Critical path length | 6-12 rooms | Retry generation |
| Boss reachability | Boss room reachable via critical path | Retry generation |
| Rest before boss | At least 1 Rest room within 3 rooms of Boss | Insert Rest room |
| Room type variety | No more than 3 consecutive same-type rooms | Swap middle room type |
| Difficulty curve | Difficulty increases monotonically on critical path | Re-sort room difficulties |
| Total room count | Within zone min/max range | Add/remove branch rooms |
| Secret placement | All secrets attached to valid parent rooms | Reattach or remove |

### Retry Policy

| Parameter | Value |
|-----------|-------|
| Max retries | 10 per zone generation call |
| Retry strategy | Increment seed by 1 on each retry |
| Target retry rate | < 5% (fewer than 5 in 100 seeds require retry) |
| Hard failure | After 10 retries, return error code -1 to Unity |

## DAG Structure

### Node (RoomData)

```rust
pub struct RoomData {
    pub id: u16,
    pub room_type: RoomType,  // enum: Combat, Puzzle, Reward, Rest, Boss, Transition, Secret
    pub width: u8,            // 40-80 tiles
    pub height: u8,           // 24-40 tiles
    pub enemy_count: u8,      // 0-20
    pub hazard_count: u8,     // 0-5
    pub loot_tier: u8,        // 0-4
    pub difficulty: u8,       // 0-100 (position on difficulty curve)
    pub room_seed: u64,       // for per-room randomization
    pub on_critical_path: bool,
}
```

### Edge (EdgeData)

```rust
pub struct EdgeData {
    pub from: u16,
    pub to: u16,
    pub edge_type: EdgeType,  // Standard, Shortcut, Secret
    pub weight: u8,
    pub bidirectional: bool,
}
```

## Performance

| Metric | Target | Measured (avg) |
|--------|--------|----------------|
| Phase 1 (Critical Path) | < 5ms | 1.2ms |
| Phase 2 (Branching) | < 10ms | 3.8ms |
| Phase 3 (Loops) | < 5ms | 1.1ms |
| Phase 4 (Secrets) | < 5ms | 0.9ms |
| Phase 5 (Validation) | < 10ms | 2.4ms |
| Total generation | < 50ms | 9.4ms |
| Serialization | < 5ms | 1.8ms |

## Implementation Notes

- Algorithm implemented in `eots-procgen-zone/src/generator.rs`
- Each phase is a separate function taking `&mut ZoneGraph` and `&mut Xoshiro256StarStar`
- Validation returns `Vec<ValidationError>` consumed by retry logic in `generate_zone()`
- Serialization uses custom binary format (not serde) for minimal overhead
- Output buffer size: typically 2-4 KB per zone graph (well within 64KB FFI buffer)
- Determinism verified by generating 10,000 identical zones from same seed across Rust and C# PRNG
