# Bridge Layer Specification

## Purpose
The Bridge layer is the **only place** where Unity API calls interact with the custom ECS. It translates between pure C# simulation (FixedPoint64, ECS) and Unity's rendering/input/audio systems (float, GameObjects, MonoBehaviours).

```
┌─────────────────────────────────────────────┐
│  UNITY (float world)                        │
│  GameObjects, SpriteRenderers, Input System  │
├─────────────── BRIDGE LAYER ────────────────┤
│  SimulationRunner, RenderProxy, InputBridge  │
│  EntitySpawner, AudioBridge, CameraBridge    │
│  ──── FP64 ↔ float conversion happens HERE ──│
├─────────────────────────────────────────────┤
│  CORE ECS (FixedPoint64 world)              │
│  EotSWorld, Components, Systems, Netcode    │
└─────────────────────────────────────────────┘
```

## SimulationRunner.cs

```csharp
public class SimulationRunner : MonoBehaviour
{
    private EotSWorld _world;
    private EotSWorld _previousWorld;  // For interpolation
    private int _currentTick;
    private float _accumulator;

    private const float TICK_DURATION = 1f / 60f;  // 16.666ms

    void Awake()
    {
        _world = new EotSWorld(MAX_ENTITIES: 4096);
        RegisterSystems(_world);
    }

    void FixedUpdate()
    {
        // FixedUpdate rate matches sim rate (60Hz)
        _previousWorld = _world.Snapshot();
        var inputs = InputBridge.GatherInputs();
        _world.Tick(new TickContext
        {
            CurrentTick = _currentTick++,
            DeltaTime = FixedPoint64.TickRate,
            Inputs = inputs
        });
    }

    // Called by RenderProxy for visual interpolation
    public float GetInterpolationAlpha()
    {
        return _accumulator / TICK_DURATION;
    }
}
```

**Script Execution Order:** SimulationRunner = -100 (runs before everything)

## RenderProxy.cs

```csharp
public class RenderProxy : MonoBehaviour
{
    [SerializeField] private SpriteRenderer _spriteRenderer;
    private int _entityId;
    private SimulationRunner _sim;

    // Previous and current positions for lerp
    private Vector2FP _prevPosition;
    private Vector2FP _currPosition;

    void LateUpdate()
    {
        float alpha = _sim.GetInterpolationAlpha();

        // THIS IS THE ONLY FP64→float CONVERSION
        float x = Mathf.Lerp(_prevPosition.X.ToFloat(), _currPosition.X.ToFloat(), alpha);
        float y = Mathf.Lerp(_prevPosition.Y.ToFloat(), _currPosition.Y.ToFloat(), alpha);

        transform.position = new Vector3(x, y, 0f);
    }
}
```

## InputBridge.cs

```csharp
public class InputBridge : MonoBehaviour
{
    private PlayerInput _playerInput;

    public InputCommand GatherInput()
    {
        var move = _playerInput.actions["Move"].ReadValue<Vector2>();
        var aim = _playerInput.actions["Aim"].ReadValue<Vector2>();

        return new InputCommand
        {
            // float→FP64 conversion at input boundary
            MoveX = FixedPoint64.FromFloat(move.x),
            MoveY = FixedPoint64.FromFloat(move.y),
            AimX = FixedPoint64.FromFloat(aim.x),
            AimY = FixedPoint64.FromFloat(aim.y),
            Buttons = PackButtons()  // Bitfield: jump, dash, attack, heavy, ability, fusion
        };
    }
}
```

## EntitySpawner.cs

Maintains a bidirectional lookup: `entityId ↔ GameObject`

```csharp
public class EntitySpawner : MonoBehaviour
{
    private Dictionary<int, GameObject> _entityToGO = new();
    private PrefabRegistry _prefabs;

    // Listens to ECS events
    void OnEntityCreated(EntityCreatedEvent e)
    {
        var prefab = _prefabs.GetPrefab(e.EntityType);
        var go = Instantiate(prefab);
        go.GetComponent<RenderProxy>().Initialize(e.EntityId, _sim);
        _entityToGO[e.EntityId] = go;
    }

    void OnEntityDestroyed(EntityDestroyedEvent e)
    {
        if (_entityToGO.TryGetValue(e.EntityId, out var go))
        {
            Destroy(go);
            _entityToGO.Remove(e.EntityId);
        }
    }
}
```

## Rules
1. Bridge scripts live in `Assets/Scripts/EotS.Bridge/`
2. Bridge has its own Assembly Definition: `EotS.Bridge.asmdef`
3. Bridge references both `EotS.Core` and UnityEngine
4. `EotS.Core` NEVER references `EotS.Bridge` (one-way dependency)
5. All FP64↔float conversions happen ONLY in Bridge code
6. Bridge scripts use `[DefaultExecutionOrder]` to ensure correct ordering
