# Input System Architecture

## Unity Input System → ECS InputCommand

```
Unity Input System (float, event-driven)
  → InputBridge.cs (Bridge layer)
    → Converts float→FixedPoint64
    → Packs into InputCommand struct
      → Sent to ECS each tick (60Hz)
        → InputSystem processes in ECS
```

## InputCommand Struct

```csharp
public struct InputCommand
{
    public FixedPoint64 MoveX;       // -1.0 to 1.0
    public FixedPoint64 MoveY;       // -1.0 to 1.0
    public FixedPoint64 AimX;        // Aim direction X
    public FixedPoint64 AimY;        // Aim direction Y
    public ushort Buttons;           // Bitfield (16 buttons)
    public byte Tick;                // Tick this input belongs to
}
```

## Button Bitfield

| Bit | Action | Controller | Keyboard |
|-----|--------|-----------|----------|
| 0 | Jump | A / Cross | Space |
| 1 | Dash | B / Circle | Shift |
| 2 | Light Attack | X / Square | J / LMB |
| 3 | Heavy Attack | Y / Triangle | K / RMB |
| 4 | Ability 1 | LB / L1 | Q |
| 5 | Ability 2 | RB / R1 | E |
| 6 | Ability 3 | LT / L2 | R |
| 7 | Heal (Bind) | D-pad Down | F |
| 8 | Fusion Request | D-pad Up | T |
| 9 | Spore Plan | D-pad Left | G |
| 10 | Interact | D-pad Right | W near object |
| 11 | Clip Mode | Select / Touch | F9 |
| 12-15 | Reserved | — | — |

## Input Buffering

| Feature | Value | Purpose |
|---------|-------|---------|
| Jump buffer | 6 frames (100ms) | Pressed slightly early, still jumps on land |
| Coyote time | 6 frames (100ms) | Walk off edge, still can jump briefly |
| Dash buffer | 4 frames (66ms) | Queue dash during recovery |
| Attack buffer | 4 frames (66ms) | Queue next attack in combo |

## Rebinding
All actions fully remappable via Unity Input System's rebinding API. Saved to PlayerPrefs per platform. Controller and keyboard mappings independent.

## Network Considerations
- InputCommand is ~24 bytes serialized
- Sent to server every tick (60 per second)
- Redundant: last 3 ticks sent per packet (handles packet loss)
- Server applies input at the tick it belongs to, not arrival time
