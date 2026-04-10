# 24 — DATA SCHEMAS & SYSTEM INTERFACES

## ECS Component Registry

| Component | Fields | Size (bytes) | Notes |
|---|---|---|---|
| TransformComponent | x: FP64, y: FP64, rotation: FP64, scale: FP64 | 32 | All fixed-point |
| VelocityComponent | vx: FP64, vy: FP64 | 16 | |
| HealthComponent | current: i32, max: i32, damage_queue: [DamageEvent; 8] | 72 | |
| InfectionComponent | level: u8, accumulator: FP64, source_flags: u8 | 10 | |
| PlayerInputComponent | inputs: [InputCommand; 8] (ring buffer) | 256 | 8 buffered |
| FusionComponent | state: u8, partner_ids: [u32; 3], role: u8, timer: u16 | 18 | |
| ColliderComponent | x: FP64, y: FP64, w: FP64, h: FP64 | 32 | AABB |
| CharacterStateComponent | state: u8, timer: u16 | 3 | Grounded/Airborne/WallSlide/Dash |
| CombatStateComponent | state: u8, anim_tick: u16 | 3 | Ready/Attacking/Cooldown |
| HitboxComponent | offset_x: FP64, offset_y: FP64, w: FP64, h: FP64, damage: i32, lifetime: u16, owner: u32, hit_list: [u32; 8] | 84 | |
| BoonSlotComponent | slots: [BoonId; 5], rarities: [u8; 5] | 10 | |
| FunctionSlotComponent | actives: [FuncId; 4], upgrades: [FuncId; 6], passives: [FuncId; 4] | 14 | |

## InputCommand Struct
```
struct InputCommand {
    tick: u32,          // 4 bytes
    movement: FVec2,    // 16 bytes (2× FP64)
    aim: FVec2,         // 16 bytes
    ability_flags: u16, // 2 bytes (bitfield: bit 0-3 = functions, bit 4 = heal, bit 5-6 = reserved)
    fusion_request: u8, // 1 byte (bool)
    padding: u8,        // 1 byte alignment
}                       // Total: 40 bytes
```

## Supabase Schema (PostgreSQL)

### players
| Column | Type | Constraints |
|---|---|---|
| id | UUID | PK, default gen_random_uuid() |
| display_name | VARCHAR(32) | UNIQUE, NOT NULL |
| created_at | TIMESTAMPTZ | DEFAULT now() |
| spore_pass_active | BOOLEAN | DEFAULT false |
| spore_pass_expiry | TIMESTAMPTZ | NULLABLE |
| total_echoes | BIGINT | DEFAULT 0 |

### servers
| Column | Type | Constraints |
|---|---|---|
| id | UUID | PK |
| name | VARCHAR(64) | NOT NULL |
| region | VARCHAR(16) | NOT NULL |
| created_at | TIMESTAMPTZ | DEFAULT now() |
| player_count | INTEGER | DEFAULT 0 |
| status | ENUM('active','locked','archived') | DEFAULT 'active' |
| true_ending_vote | ENUM('pending','feed','starve') | DEFAULT 'pending' |

### skill_tree_nodes
| Column | Type | Constraints |
|---|---|---|
| id | SERIAL | PK |
| server_id | UUID | FK → servers.id |
| branch | ENUM('expansion','evolution','ecology','infrastructure','lore') | NOT NULL |
| depth | SMALLINT | 1-20 |
| name | VARCHAR(64) | NOT NULL |
| echo_cost | BIGINT | floor(1000 × 1.8^depth) |
| echoes_contributed | BIGINT | DEFAULT 0 |
| is_unlocked | BOOLEAN | DEFAULT false |
| unlocked_at | TIMESTAMPTZ | NULLABLE |
| prerequisite_id | INTEGER | FK → self, NULLABLE |

### contributions
| Column | Type | Constraints |
|---|---|---|
| id | BIGSERIAL | PK |
| player_id | UUID | FK → players.id |
| server_id | UUID | FK → servers.id |
| node_id | INTEGER | FK → skill_tree_nodes.id |
| amount | BIGINT | NOT NULL |
| multiplier | NUMERIC(3,1) | 1.0 or 2.0 |
| effective_amount | BIGINT | amount × multiplier |
| contributed_at | TIMESTAMPTZ | DEFAULT now() |

## Redis Key Patterns
| Pattern | Type | TTL | Content |
|---|---|---|---|
| session:{id} | Hash | 3600s | server_id, zone_id, room_seed, player_count, state, tick |
| session:{id}:players | Set | 3600s | player_id UUIDs |
| session:{id}:infection:{player_id} | String | 3600s | u8 (0-100) |
| session:{id}:fusion | Hash | 3600s | state, pilot_id, gunner_id, support_id, relay_id |
| player:{player_id}:presence | Hash | 300s | server_id, session_id, status, last_seen |
| matchmaking:{server_id}:{zone_id} | Sorted Set | — | score=queue_time, member=player_id |
| canopy:{server_id}:positions | Hash | — | player_id → packed_x_y |
