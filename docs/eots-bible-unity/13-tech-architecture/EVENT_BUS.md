# Event Bus System

## Purpose
Decoupled communication between ECS systems without direct references. Systems publish events, other systems subscribe. Events are processed in order within the same tick.

## Interface

```csharp
namespace EotS.Core.ECS
{
    public interface IEvent { }

    public class EventBus
    {
        private Dictionary<Type, List<Action<IEvent>>> _handlers;
        private Queue<IEvent> _pendingEvents;

        public void Subscribe<T>(Action<T> handler) where T : IEvent;
        public void Publish<T>(T evt) where T : IEvent;
        public void ProcessAll();  // Called once per tick, after all systems
    }
}
```

## Core Events

| Event | Published By | Consumed By | Data |
|-------|-------------|-------------|------|
| EntityCreatedEvent | EotSWorld | EntitySpawner (Bridge) | entityId, entityType |
| EntityDestroyedEvent | EotSWorld | EntitySpawner (Bridge) | entityId |
| DamageEvent | CombatSystem | UI (damage numbers), AudioBridge | sourceId, targetId, amount, isCrit |
| DeathEvent | CombatSystem | SpawnSystem, AudioBridge, NarratorManager | entityId, killerEntityId, overloadCount |
| BoonAcquiredEvent | BoonSystem | UI, AudioBridge | playerId, boonId, rarity |
| FusionStateEvent | FusionSystem | RenderProxy, AudioBridge, UI | state, playerIds |
| InfectionTierEvent | InfectionSystem | AudioBridge (DSP), RenderProxy (shader) | playerId, newTier |
| RoomEnteredEvent | RoomManager | AISystem (wake), AudioBridge (music), ProcGen | roomId, roomType |
| BossPhaseEvent | BossSystem | AudioBridge (music), CameraShake | bossId, phase |
| CommunityTreeEvent | BackendClient | UI, CameraScript | branch, newDepth |

## Rules
1. Events are **fire-and-forget** — publisher doesn't know who listens
2. Events are **processed in order** within a tick
3. Events are **struct types** — no heap allocation
4. Events MUST NOT contain Unity API types (pure C#)
5. Bridge layer subscribes to events and translates to Unity calls
6. Event queue is cleared after ProcessAll() each tick
