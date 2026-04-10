# 15 — PROCEDURAL GENERATION & CONTENT VALIDATION

## Architecture: Rust Crate → C FFI (native) + WASM (tools)

### Crate Structure
```
procgen/
├── crates/
│   ├── eots-procgen-core/      (no_std, data types, RNG)
│   ├── eots-procgen-generator/  (generation algorithm)
│   ├── eots-procgen-ffi/        (C FFI for UE5)
│   └── eots-procgen-wasm/       (WASM for web tools)
```

## Generation Algorithm (5 Phases)

### Phase 1: Critical Path
Generate linear path from Entrance to Boss. Length L = RNG.range(n/3, 2n/3). Must include at least 1 Combat and 1 Puzzle/Reward room before Boss.

### Phase 2: Branching
For each critical path room: with probability (0.4 + 0.2 × zone.density_factor), generate a branch of length RNG.range(1, remaining/3).

### Phase 3: Loop Connections
For each pair of rooms with graph distance > 3: with probability 0.15, add shortcut edge if max node degree ≤ 4.

### Phase 4: Secret Placement
Attach Secret rooms to random non-Boss/non-Entrance rooms. Exactly 1 connection, marked hidden.

### Phase 5: Validation (5 Constraints)
1. **Reachability:** BFS from entrance reaches all non-secret rooms.
2. **Boss Gating:** Every path entrance→boss includes ≥1 Combat and ≥1 Puzzle/Reward.
3. **Rest Spacing:** No path of length >5 without Rest or Reward room.
4. **Transition Anchoring:** Transition rooms at graph boundary, degree ≤ 2.
5. **Critical Path Length:** In [floor(n/3), floor(2n/3)].

If validation fails: increment seed suffix, retry. Max 10 retries.

## Room Type Probability Weights (per Zone)
| Type | Sporehaven | Cortex | Ferment | Chitin | Rot | Forge | Necroveil | Core |
|---|---|---|---|---|---|---|---|---|
| Combat | 30 | 25 | 20 | 40 | 25 | 25 | 30 | 35 |
| Puzzle | 10 | 20 | 25 | 5 | 15 | 25 | 15 | 10 |
| Reward | 20 | 15 | 15 | 15 | 15 | 10 | 10 | 10 |
| Rest | 15 | 10 | 10 | 10 | 15 | 10 | 5 | 5 |
| Boss | 5 | 5 | 5 | 10 | 5 | 5 | 10 | 20 |
| Transition | 10 | 15 | 15 | 10 | 15 | 15 | 15 | 10 |
| Secret | 10 | 10 | 10 | 10 | 10 | 10 | 15 | 10 |

## Micro-Gate Ability Selection
Abilities from current zone's lock + previous 1-2 zones' locks, weighted 3:2:1 by recency.

## Quality Target
Generate 1000 zones per config with random seeds. ALL must pass validation. Retry rate target: <5%.
