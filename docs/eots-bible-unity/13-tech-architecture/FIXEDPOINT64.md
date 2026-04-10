# FixedPoint64 Specification

## Format: 32.32 Fixed-Point (int64)

```
Bit layout (64 bits total):
[S][31-bit integer part].[32-bit fractional part]
 ↑
 Sign bit

Internal storage: long (System.Int64)
Range: -2,147,483,648.0 to 2,147,483,647.999999999767
Precision: ~0.00000000023 (2^-32)
```

## Constants

```csharp
public readonly struct FixedPoint64
{
    internal readonly long RawValue;

    public static readonly FixedPoint64 Zero     = new(0L);
    public static readonly FixedPoint64 One      = new(1L << 32);          // 4294967296
    public static readonly FixedPoint64 Half     = new(1L << 31);          // 2147483648
    public static readonly FixedPoint64 Pi       = new(13493037705L);      // 3.14159265358...
    public static readonly FixedPoint64 TwoPi    = new(26986075409L);
    public static readonly FixedPoint64 HalfPi   = new(6746518852L);
    public static readonly FixedPoint64 E        = new(11674931555L);      // 2.71828182845...
    public static readonly FixedPoint64 Epsilon  = new(1L);               // Smallest positive
    public static readonly FixedPoint64 MaxValue = new(long.MaxValue);
    public static readonly FixedPoint64 MinValue = new(long.MinValue);

    // Game-specific constants
    public static readonly FixedPoint64 Gravity       = FromInt(-20);      // -20.0 units/s²
    public static readonly FixedPoint64 TerminalVel   = FromInt(12);       // 12.0 units/tick
    public static readonly FixedPoint64 TickRate       = FromRatio(1, 60); // 1/60
}
```

## Operations

### Arithmetic
```csharp
// Addition/Subtraction: direct long add/sub (overflow-safe)
public static FixedPoint64 operator +(FixedPoint64 a, FixedPoint64 b)
    => new(a.RawValue + b.RawValue);

// Multiplication: shift right 32 after multiply to maintain scale
public static FixedPoint64 operator *(FixedPoint64 a, FixedPoint64 b)
    => new((long)(((Int128)a.RawValue * b.RawValue) >> 32));

// Division: shift left 32 before divide
public static FixedPoint64 operator /(FixedPoint64 a, FixedPoint64 b)
    => new((long)(((Int128)a.RawValue << 32) / b.RawValue));
```

### Trigonometry (Lookup Table)
```csharp
// 1024-entry sin table, linear interpolation between entries
// Table generated at compile time from double precision, stored as long[]
public static FixedPoint64 Sin(FixedPoint64 angle);
public static FixedPoint64 Cos(FixedPoint64 angle);
public static FixedPoint64 Atan2(FixedPoint64 y, FixedPoint64 x);
```

### Square Root (Binary Search)
```csharp
// Newton-Raphson with 8 iterations, converges for all positive values
public static FixedPoint64 Sqrt(FixedPoint64 value);
```

## Conversion

```csharp
public static FixedPoint64 FromInt(int value) => new((long)value << 32);
public static FixedPoint64 FromRatio(int num, int den) => new(((long)num << 32) / den);
public int ToInt() => (int)(RawValue >> 32);

// ONLY used in Bridge layer (RenderProxy)
public float ToFloat() => (float)RawValue / (1L << 32);
```

## Vector2FP

```csharp
public struct Vector2FP
{
    public FixedPoint64 X;
    public FixedPoint64 Y;

    public FixedPoint64 Magnitude() => FixedPoint64.Sqrt(X * X + Y * Y);
    public Vector2FP Normalized();
    public static FixedPoint64 Dot(Vector2FP a, Vector2FP b);
    public static FixedPoint64 Distance(Vector2FP a, Vector2FP b);
}
```

## Determinism Guarantee

The PRNG (xoshiro256**) and all FixedPoint64 operations produce **bit-identical results** across:
- Windows x86_64
- Linux x86_64
- macOS ARM64 (Apple Silicon)
- PS5 (x86_64)
- Xbox Series (x86_64)
- Switch (ARM64)

Verified by determinism test harness: run 10,000 ticks with identical inputs, hash final world state, compare across all platforms.

## Rules
1. **NEVER** convert to float inside Core/ — floats are only in Bridge/
2. **NEVER** use Math.Sin/Cos/Sqrt — always FixedPoint64 versions
3. **ALWAYS** use FixedPoint64 for positions, velocities, timers, damage multipliers
4. **Int is OK** for: entity IDs, HP values, tick counts, enum values
