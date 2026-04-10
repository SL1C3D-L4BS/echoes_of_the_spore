# Fungal Fusion -- Full Specification

## State Machine

```
NONE → REQUESTING → MERGING → FUSED → SEPARATING → NONE
```

## State Transition Table

| From | To | Trigger | Duration |
|---|---|---|---|
| NONE | REQUESTING | Both players send `fusion_request=true` within same tick, distance <= 3.0 units | Instant |
| REQUESTING | MERGING | All requesters remain within 3.0 units for 30 ticks (500ms) | 30 ticks |
| REQUESTING | NONE | Any requester moves beyond 3.0 units OR sends `fusion_cancel=true` | Instant |
| MERGING | FUSED | `fusion_timer` counts down from 60 ticks (1s) to 0 | 60 ticks |
| MERGING | NONE | Any partner dies during merge animation | Instant |
| FUSED | SEPARATING | Pilot sends `fusion_request=true` (toggle) | Instant |
| FUSED | NONE | Fused entity HP reaches 0 (all partners die) | Instant |
| SEPARATING | NONE | `fusion_timer` counts down from 30 ticks (500ms) to 0 | 30 ticks |

## Proximity Check

- Distance metric: Euclidean XZ-plane (Y ignored for fusion range)
- Threshold: 3.0 world units
- Checked every tick during REQUESTING state
- During MERGING, partners are interpolated toward centroid; no proximity re-check needed

## Timers

| Timer | Ticks | Real Time | Context |
|---|---|---|---|
| Request hold | 30 | 500ms | Players must sustain request |
| Merge animation | 60 | 1000ms | Visual blend, collider interpolation |
| Separate animation | 30 | 500ms | Entity split, position offset |
| Fusion cooldown | 300 | 5000ms | After separation, cannot re-fuse |

## Stat Formulas

### Fused Entity Stats

```
Fused_HP       = floor(sum(partner_HP) * 1.15)
Fused_MaxHP    = floor(sum(partner_MaxHP) * 1.15)
Fused_Position = centroid(partner_positions)           // mean of X, Y, Z
Fused_Collider = AABB_union(partner_colliders) * 1.10  // 10% padding
Fused_Infection = floor(mean(partner_infections))
Fused_Speed    = min(partner_speeds) * 0.85            // 15% slower than slowest
Fused_Damage   = max(partner_base_damage) * 1.25       // 25% bonus on top hit
```

### HP Split on Separation

```
for each partner:
    partner_HP = floor(Fused_HP * (partner_original_MaxHP / sum(all_partner_original_MaxHP)))
```

Remaining HP from rounding distributed to Pilot.

## Collider Merging

1. Compute AABB union of all partner colliders at their merge-start positions
2. Scale result by 1.10 on each axis (padding for visual overlap)
3. Assign a single `CapsuleCollider` fitted to the AABB for physics
4. Hurtbox uses the same capsule
5. Hitbox per attack remains attack-specific (defined by Gunner/Support abilities)

## Dual Brain Control Scheme

| Player Count | Pilot | Gunner | Support | Relay |
|---|---|---|---|---|
| 2P | Movement, dodge, Spore Plan trigger | Aim, all abilities | -- | -- |
| 3P | Movement, dodge | Aim, offensive abilities | Defensive abilities, buffs | -- |
| 4P | Movement, dodge | Aim, primary attack | Defensive, crowd control | Support abilities, resource mgmt, Spore-Talk relay |

### Role Assignment

- Pilot: player who initiated the fusion request first (tie-break: lowest player_id)
- Gunner: second player to accept
- Support/Relay: third and fourth in order of acceptance

### Role Swap

- During FUSED state, Pilot can transfer Pilot role to any partner via `role_swap` input
- 120-tick (2s) cooldown on role swaps
- Swap is instant; no animation interruption

## Ability Routing

| Source | Slot 1 | Slot 2 | Slot 3 | Slot 4 |
|---|---|---|---|---|
| Pilot | Dodge (mapped) | -- | -- | Spore Plan |
| Gunner | Primary Attack | Secondary Attack | Ability 1 | -- |
| Support | Shield/Heal | CC Ability | Ability 2 | -- |
| Relay | Buff 1 | Buff 2 | Resource Pulse | Spore-Talk relay |

Unmapped slots from absent roles (2P/3P) are disabled on the fused entity.

## Edge Cases

| Scenario | Resolution |
|---|---|
| Partner disconnects during FUSED | Fused entity persists with remaining partners. Stats recalculated minus disconnected partner. |
| Fused entity enters boss room | Allowed. Boss HP scaling applies to fused entity as single entity (not per-partner). |
| Two 2P fused entities request fusion | Not allowed. Max fusion size is 4 individual players. |
| Fused entity falls into void | All partners die. Respawn as individuals. |
| Infection threshold crossed while fused | Uses Fused_Infection (mean). Individual infection unchanged until separation. |

## Network Sync

- Fused entity uses a single `NetworkObject` with `FusionState` component
- Original player `NetworkObject`s become `FusionGhost`: authority transferred to server, no physics, no rendering
- Input from all partners routed to fused entity's `FusionInputBuffer` (role-tagged)
- Tick-aligned: all partner inputs for tick N processed together before simulation step
