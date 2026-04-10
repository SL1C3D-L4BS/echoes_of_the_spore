# Behavior Trees -- AI Framework

## Overview

Tier 2+ enemies use a custom behavior tree (BT) framework built on Unity's ECS. The framework provides selector, sequence, and decorator nodes with a utility scoring layer for dynamic decision-making.

## Node Types

### Core Nodes

| Node Type | Behavior | Success | Failure |
|---|---|---|---|
| Selector | Evaluates children left-to-right, returns on first success | Any child succeeds | All children fail |
| Sequence | Evaluates children left-to-right, returns on first failure | All children succeed | Any child fails |
| Parallel | Evaluates all children simultaneously | Policy-dependent (all/any) | Policy-dependent (all/any) |

### Decorator Nodes

| Decorator | Behavior |
|---|---|
| Inverter | Inverts child result (success to failure, failure to success) |
| Repeater | Repeats child N times or until failure |
| Cooldown | Blocks child execution for N ticks after last success |
| Condition | Evaluates a boolean predicate; runs child if true, fails if false |
| Timeout | Fails child if execution exceeds N ticks |
| ForceSuccess | Always returns success regardless of child result |
| UntilFail | Repeats child until it fails |

### Leaf Nodes (Actions)

| Action | Description |
|---|---|
| MoveTo(target) | Navigate to target position via NavMesh |
| Attack(attack_id) | Execute specified attack pattern |
| Flee(direction) | Move away from threat source |
| Idle(duration) | Wait for specified ticks |
| Patrol(waypoints) | Follow waypoint sequence |
| LookAt(target) | Rotate to face target |
| PlayAnimation(clip) | Trigger animation state |
| SetVariable(key, val) | Write to blackboard |
| Log(message) | Debug output |

### Leaf Nodes (Conditions)

| Condition | Description |
|---|---|
| IsInRange(target, dist) | True if target within distance |
| HasLOS(target) | True if line-of-sight to target |
| HPBelow(threshold) | True if HP below percentage |
| HPAbove(threshold) | True if HP above percentage |
| IsTargetAlive(target) | True if target entity exists and HP > 0 |
| NoiseAbove(threshold) | True if noise at position exceeds threshold |
| CooldownReady(id) | True if specified cooldown has elapsed |
| HungerAbove(threshold) | True if hunger value exceeds threshold |

## Utility Scoring

Utility scoring augments the behavior tree by dynamically reordering Selector children based on scored priorities.

### Formula

```
Utility(action) = sum(Wi * Ci(context))
```

Where:
- `Wi` = weight for consideration `i`
- `Ci` = consideration function returning 0.0-1.0

### Standard Considerations

| Consideration | Function | Description |
|---|---|---|
| Proximity | `1 - (distance / max_distance)` | Closer targets score higher |
| Threat | `threat_table[target_type]` | Players = 1.0, Tier 1 = 0.3 |
| Hunger | `hunger / 100` | Drives ecosystem hunting |
| Self HP | `1 - (current_hp / max_hp)` | Low HP increases flee/heal priority |
| Target HP | `1 - (target_hp / target_max_hp)` | Weak targets score higher |
| Aggression | `aggression_level` | Personality modifier (per-instance) |

### Utility Selector Node

A specialized Selector that:
1. Scores all children using utility functions
2. Sorts children by score (descending)
3. Evaluates in sorted order (highest utility first)
4. Returns on first success

Evaluation frequency: every 30 ticks (500ms) to reduce CPU cost. Between evaluations, the last-sorted order is used.

## Tree Templates

### Tier 2 Standard Template

```
[UtilitySelector]
  ├─ [Sequence: Flee]
  │   ├─ Condition: HPBelow(0.25)
  │   ├─ Condition: IsInRange(player, 10)
  │   └─ Action: Flee(away_from_player)
  │
  ├─ [Sequence: Attack Player]
  │   ├─ Condition: IsInRange(player, detection_range)
  │   ├─ Condition: HasLOS(player)
  │   ├─ Action: MoveTo(player)
  │   ├─ Condition: IsInRange(player, attack_range)
  │   └─ Action: Attack(primary)
  │
  ├─ [Sequence: Hunt Tier 1]
  │   ├─ Condition: HungerAbove(50)
  │   ├─ Condition: IsTargetAlive(nearest_tier1)
  │   ├─ Action: MoveTo(nearest_tier1)
  │   ├─ Condition: IsInRange(nearest_tier1, attack_range)
  │   ├─ Action: Attack(primary)
  │   └─ Action: Feed(3s)
  │
  └─ [Sequence: Patrol]
      ├─ Action: Patrol(waypoints)
      └─ Action: Idle(random(60, 180) ticks)
```

### Tier 3 Phase Template

```
[Selector]
  ├─ [Condition: HPBelow(0.5)]
  │   └─ [Phase2Tree]  // separate full BT for phase 2
  │
  └─ [Phase1Tree]      // default BT for phase 1
```

Phase transitions are handled by swapping the active sub-tree reference.

## Blackboard

Each enemy has a blackboard (key-value store) for sharing data between nodes:

| Key | Type | Example |
|---|---|---|
| target | EntityRef | Current attack target |
| last_noise_pos | float3 | Position of last detected noise |
| hunger | float | Current hunger level |
| aggression | float | Personality modifier |
| phase | int | Current phase (Tier 3/Boss only) |
| home_position | float3 | Spawn/patrol center |
| alert_level | enum | Unaware, Alerted, Investigating, Engaged |

Blackboard is implemented as `NativeHashMap<FixedString32, BlackboardValue>` where `BlackboardValue` is a union type.

## Performance Optimizations

| Optimization | Detail |
|---|---|
| Tick budget | Max 50 BT evaluations per frame. Excess deferred to next frame. |
| LOD tiers | Off-screen enemies evaluate every 10 ticks (not every tick) |
| Pooled nodes | BT node instances pooled. No GC allocation during evaluation. |
| Burst compilation | All condition/action nodes are Burst-compatible `ISystem` implementations |
| Parallel evaluation | Independent enemy BTs evaluated across job worker threads |

## ECS Integration

### Components

| Component | Contents |
|---|---|
| `BehaviorTreeRef` | Index into BT definition asset (shared across same enemy type) |
| `BTExecutionState` | Current node index, running status, tick counter |
| `Blackboard` | Per-entity key-value store |
| `UtilityScores` | Cached scores for current evaluation cycle |

### Systems

| System | Schedule | Purpose |
|---|---|---|
| `BTEvaluationSystem` | Every tick | Advance running BT nodes, check conditions |
| `UtilityScoringSystem` | Every 30 ticks | Recalculate utility scores, reorder selectors |
| `BlackboardSyncSystem` | Every tick | Update blackboard from world state (target positions, HP, noise) |
| `BTDebugSystem` | Editor only | Render BT state in scene view for selected entity |

## Authoring

- BT definitions are ScriptableObjects (`BehaviorTreeAsset`)
- Visual editor: custom node graph editor in Unity (GraphView API)
- Each enemy prefab references a `BehaviorTreeAsset`
- Runtime: `BehaviorTreeAsset` baked into `BlobAsset` for Burst-compatible access
- Hot-reload: in dev builds, BT assets can be modified at runtime and take effect next evaluation cycle

## Debug Visualization

| Feature | Description |
|---|---|
| Node highlight | Currently executing node highlighted in green (scene view gizmo) |
| Blackboard inspector | Live view of all blackboard values in custom inspector |
| Utility bar chart | Bar chart of utility scores per Selector child |
| Execution history | Last 100 ticks of node execution log (ring buffer) |
| BT profiler | Per-enemy evaluation cost in microseconds |

## Implementation Notes

- Framework source: `Assets/Scripts/AI/BehaviorTree/` namespace `EchoesAI.BT`
- Node base class: `BTNode` with `Evaluate(ref BTContext)` returning `BTStatus` enum (Success, Failure, Running)
- BT definitions support inheritance: variant enemy types override specific sub-trees
- Fusion with ECS: BT nodes read/write ECS components through `BTContext` which wraps `EntityManager`
- Serialization: BT assets serialize to JSON for version control, bake to BlobAsset at build time
