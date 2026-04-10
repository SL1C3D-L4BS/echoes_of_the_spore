# Death & Overload Mechanics

## Design Intent

Death in Echoes is a three-stage degradation, not a binary state. Each overload weakens the player by disabling a random Echo Function, creating an escalating tension loop. The player becomes weaker with each death but always has a chance to recover -- until the third overload ends the run.

## Overload Sequence

| Overload # | Trigger | HP Result | Function Penalty | Echo Penalty | Narrator Response |
|------------|---------|-----------|-----------------|--------------|-------------------|
| 1st | HP reaches 0 | Refill to 100% | 1 random Active Function disabled | None | Concerned observation |
| 2nd | HP reaches 0 again | Refill to 100% | 2nd random Active Function disabled | None | Warning, tone shifts |
| 3rd | HP reaches 0 again | True death | N/A (run ends) | 50% Echo retention | Mourning, acceptance |

## Function Disable Rules

| Rule | Detail |
|------|--------|
| Selection | Random from currently equipped Active Functions |
| Cannot disable | Passive Functions, base movement, dash, basic attacks |
| Minimum Functions | Player always retains at least base kit (no Functions) |
| Visual indicator | Disabled Function slot turns grey with corruption VFX |
| UI indicator | Crossed-out icon in HUD, tooltip shows "Overloaded" |
| Restore condition | Reach a Rest room 3+ rooms away from overload location |
| Restore behavior | All disabled Functions restore simultaneously |

## Distance Tracking for Function Restore

```
overload_room_id = room where overload occurred
current_room_distance = shortest path distance from overload_room_id
restore_threshold = 3 rooms

if (current_room_distance >= restore_threshold AND room.type == REST):
    restore_all_disabled_functions()
    clear_overload_markers()
```

| Distance | At Rest Room? | Functions Restored? |
|----------|--------------|-------------------|
| 0-2 rooms | Yes | No |
| 3+ rooms | No | No (must be at Rest) |
| 3+ rooms | Yes | Yes |

## True Death (3rd Overload)

| Parameter | Value |
|-----------|-------|
| Echo retention | 50% of Echoes earned this run |
| Return location | Canopy (hub) |
| Infection | Preserved at current level |
| Boons | All lost (ephemeral) |
| Combo counter | Reset to 0 |
| Silk Gauge | Reset to 0 |
| Map progress | Preserved (rooms stay revealed) |
| Community Tree contribution | Earned Echoes still contribute at 50% rate |

## Overload Audio Design

| Overload # | Audio Sting | Duration | Character |
|------------|------------|----------|-----------|
| 1st | `sfx_overload_1` — low resonant pulse | 1.2 seconds | Mycelium hum, rising |
| 2nd | `sfx_overload_2` — distorted chord | 1.5 seconds | Dissonant, cracking |
| 3rd | `sfx_overload_3` — reverb decay | 2.0 seconds | Hollow, fading out |

Each overload sting is unique and never reused elsewhere. Players learn to associate each sound with the severity of their situation.

## Overload Visual Effects

| Overload # | Screen Effect | Character Effect | Duration |
|------------|--------------|-----------------|----------|
| 1st | White flash (200 ms), vignette pulse | Brief golden shimmer | 0.5s total |
| 2nd | Red flash (300 ms), screen crack overlay | Corruption tendrils flicker | 0.8s total |
| 3rd | Full-screen whiteout, slow fade to black | Character dissolves into spores | 2.0s transition |

## Mycelium Narrator Lines (Per Overload)

| Overload # | Line Pool Size | Tone | Example Context |
|------------|---------------|------|-----------------|
| 1st | 8 lines | Concern, curiosity | Narrator notices the player's strain |
| 2nd | 8 lines | Urgency, warning | Narrator pleads caution |
| 3rd | 8 lines | Mourning, acceptance | Narrator acknowledges the loss |

Lines are selected based on current zone, infection level, and which Functions were disabled. No line repeats within the same run.

## Multiplayer Death Rules

| Scenario | Behavior |
|----------|----------|
| Co-op (unfused), 1 player overloads | Overloaded player continues with penalty; partner unaffected |
| Co-op (unfused), 1 player true-dies | Dead player spectates; living player continues solo |
| Fused state, HP reaches 0 | Fusion breaks, both players take overload independently |
| All players true-die | Run ends for party, all return to Canopy |
| Spectator options | Free camera, partner camera lock, ping system |

## Interaction with Heat Conditions

| Condition | Death Interaction |
|-----------|------------------|
| Symbiotic Decay | Lower max HP means faster overloads |
| Sealed Roots | Fewer Rest rooms = harder to restore Functions |
| The Rot Ascends | Boss Phase 3 starts at 50%, more lethal |
| Haunted Echoes | Revived enemies can trigger overloads on weakened players |

## ECS Implementation

```csharp
public struct OverloadComponent
{
    public int OverloadCount;          // 0, 1, 2 (3 = true death event)
    public int OverloadRoomId;         // Room where last overload occurred
    public int DisabledFunctionSlot1;  // -1 if not disabled
    public int DisabledFunctionSlot2;  // -1 if not disabled
    public bool PendingRestore;        // True when at valid Rest room
}
// OverloadSystem triggers on HP <= 0 events
// FunctionRestoreSystem checks distance + room type each room entry
```
