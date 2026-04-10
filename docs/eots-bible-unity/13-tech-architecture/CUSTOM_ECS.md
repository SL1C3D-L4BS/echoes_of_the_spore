# Custom ECS Architecture

## Why Not Unity DOTS

| Concern | DOTS | Our Custom ECS |
|---------|------|---------------|
| **Determinism** | Uses float (IEEE 754 rounding varies per platform) | FixedPoint64 (int64), bit-identical everywhere |
| **Complexity** | Burst, Jobs, managed/unmanaged split, EntityManager | ~2,000 lines pure C#, one World class |
| **Rollback** | Not designed for snapshot/restore | Built-in snapshot serialization |
| **Dependencies** | Requires Unity packages, IL2CPP nuances | Zero Unity API deps in Core/ |
| **Entity count** | Optimized for millions | We need <4,096 per room |
| **Learning curve** | Steep (Burst attributes, NativeContainers) | Standard C# structs and loops |

## Architecture

```
EotSWorld
├── EntityPool (int[] freeList, int nextId)
├── ComponentStorage<T> (Dictionary<int, T> where T : struct)
├── SystemPipeline (List<ISystem> ordered)
├── EventBus (Queue<IEvent>)
└── SnapshotBuffer (RingBuffer<WorldSnapshot> capacity=120)
```

## Core Interfaces

```csharp
namespace EotS.Core.ECS
{
    public interface IComponent { }

    public interface ISystem
    {
        void Execute(EotSWorld world, TickContext ctx);
        int Order { get; }  // Lower = runs first
    }

    public interface IEvent { }

    public struct TickContext
    {
        public int CurrentTick;
        public FixedPoint64 DeltaTime;  // Always 1/60
        public InputCommand[] Inputs;    // One per player
    }
}
```

## Component Storage (SoA)

```csharp
public class ComponentStorage<T> where T : struct, IComponent
{
    private T[] _data;           // Contiguous array
    private int[] _entityIndex;  // entityId → array index
    private int _count;

    public ref T Get(int entityId);
    public void Set(int entityId, T component);
    public bool Has(int entityId);
    public void Remove(int entityId);
    public ReadOnlySpan<T> All();  // For system iteration
}
```

## System Execution Order

| Order | System | Responsibility |
|-------|--------|---------------|
| 0 | InputSystem | Apply InputCommands to entities |
| 10 | MovementSystem | Integrate velocity, apply gravity |
| 20 | CollisionSystem | AABB detection, spatial hash, response |
| 30 | CombatSystem | Hitbox/hurtbox overlap, damage application |
| 40 | StatusEffectSystem | Tick durations, apply effects |
| 50 | InfectionSystem | Update infection levels, stat mods |
| 60 | FusionSystem | Manage fusion state machine |
| 70 | AISystem | Tier 1 FSM, Tier 2 BT, Tier 3 phases |
| 80 | SpawnSystem | Process spawn/destroy queues |
| 90 | SnapshotSystem | Serialize current state to ring buffer |

## Entity Lifecycle

```
Create: world.CreateEntity() → returns int entityId
  → Allocates from freeList or increments nextId
  → Fires EntityCreatedEvent

Add Component: world.AddComponent<T>(entityId, component)
  → Stores in ComponentStorage<T>

Remove Component: world.RemoveComponent<T>(entityId)
  → Removes from storage, fires event

Destroy: world.DestroyEntity(entityId)
  → Removes all components
  → Returns entityId to freeList
  → Fires EntityDestroyedEvent
```

## Query API

```csharp
// Iterate all entities with Transform + Velocity
world.Query<TransformComponent, VelocityComponent>((entityId, ref transform, ref velocity) =>
{
    transform.Position += velocity.Linear * ctx.DeltaTime;
});

// Query with optional component
world.QueryOptional<TransformComponent, HealthComponent>((entityId, ref transform, health) =>
{
    if (health.HasValue)
        // Entity has health
});
```

## Constraints
1. All components MUST be `struct` (value type)
2. No `class` fields in components (no heap references)
3. No Unity API calls in any file under `Core/ECS/`
4. All numeric fields use `FixedPoint64` (long internally)
5. Maximum 4,096 entities per world instance
6. Component storage pre-allocates for MAX_ENTITIES on world creation
