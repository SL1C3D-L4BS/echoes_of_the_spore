# Spatial Hash Grid — Collision Broadphase

## Purpose
O(1) average lookup for collision candidates. Replaces Unity's physics broadphase since we disabled Unity physics.

## Parameters

| Parameter | Value | Rationale |
|-----------|-------|-----------|
| Cell size | 4×4 game units | 2x largest expected collider (2×2 boss) |
| Grid dimensions | 256×256 cells | Covers 1024×1024 unit world space |
| Max entities per cell | 16 | Stack-allocated array, no heap |
| Update frequency | Every tick (60Hz) | Entities move, must re-hash |

## Data Structure

```csharp
public struct SpatialHashGrid
{
    private ushort[,][] _grid;  // [x,y] → entity ID array
    private int[,] _counts;     // [x,y] → entity count in cell

    public void Clear();
    public void Insert(int entityId, AABB bounds);
    public void Query(AABB bounds, Span<int> results, out int count);
}
```

## Hash Function

```csharp
// Convert world position to grid cell
int cellX = (int)(position.X.RawValue >> (32 + 2));  // Divide by 4 (cell size)
int cellY = (int)(position.Y.RawValue >> (32 + 2));
cellX = Math.Clamp(cellX, 0, 255);
cellY = Math.Clamp(cellY, 0, 255);
```

## Collision Pipeline (Per Tick)

```
1. SpatialHashGrid.Clear()                    // ~0.1ms
2. For each entity with CollisionComponent:
   a. Compute AABB from position + size       // O(n)
   b. SpatialHashGrid.Insert(id, aabb)        // O(1) per entity
3. For each entity with HitboxComponent:
   a. Query grid for overlapping cells         // O(1) average
   b. For each candidate: narrow-phase AABB test  // O(k) where k = candidates
   c. Generate CollisionEvent if overlap       // Push to EventBus
4. CollisionSystem processes events            // Apply knockback, damage
```

## Performance Budget

| Operation | Budget | Measured (500 entities) |
|-----------|--------|------------------------|
| Clear | < 0.1ms | 0.05ms |
| Insert all | < 0.2ms | 0.12ms |
| Query all hitboxes | < 0.3ms | 0.18ms |
| Narrow phase | < 0.2ms | 0.10ms |
| **Total collision** | **< 0.8ms** | **0.45ms** |

## AABB Struct

```csharp
public struct AABB
{
    public Vector2FP Min;  // Bottom-left
    public Vector2FP Max;  // Top-right

    public bool Overlaps(AABB other)
    {
        return Min.X <= other.Max.X && Max.X >= other.Min.X
            && Min.Y <= other.Max.Y && Max.Y >= other.Min.Y;
    }

    public static AABB FromCenter(Vector2FP center, Vector2FP halfSize)
    {
        return new AABB
        {
            Min = center - halfSize,
            Max = center + halfSize
        };
    }
}
```

## Collision Layers

| Layer | Bit | Collides With |
|-------|-----|---------------|
| Player | 0x01 | Enemy, Hazard, Projectile |
| Enemy | 0x02 | Player, PlayerProjectile |
| PlayerProjectile | 0x04 | Enemy |
| EnemyProjectile | 0x08 | Player |
| Hazard | 0x10 | Player, Enemy |
| Pickup | 0x20 | Player |
| Trigger | 0x40 | Player (non-physical) |

```csharp
public static bool ShouldCollide(byte layerA, byte layerB)
{
    return (_collisionMatrix[layerA] & layerB) != 0;
}
```
