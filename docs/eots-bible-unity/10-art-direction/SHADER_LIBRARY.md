# Shader Library

## Overview

All shaders target Unity URP (Universal Render Pipeline) and are authored in Shader Graph unless noted. Performance budgets assume a mid-range GPU (GTX 1060 / RX 580 equivalent) at 1080p 60fps. All shaders use half-precision where possible.

## Shader Inventory

| Shader | Type | Draw Calls | GPU Cost Target |
|--------|------|-----------|----------------|
| InfectionPulse | Sprite overlay | 1 per character | < 0.3ms |
| FossilMuralReveal | World surface | 1 per mural | < 0.2ms |
| Bioluminescence | Environment emissive | Batched per zone | < 0.5ms total |
| AcidPool | Animated surface | 1 per pool instance | < 0.15ms |
| DecayEntropy | Dissolve / death | 1 per dying entity | < 0.2ms |

## InfectionPulse Shader

Applied as an overlay layer on character sprites. Intensity scales with Infection level.

### Parameters

| Parameter | Type | Range | Default | Description |
|-----------|------|-------|---------|-------------|
| _InfectionLevel | Float | 0.0 - 1.0 | 0.0 | Maps to Infection 0-100 |
| _PulseSpeed | Float | 0.5 - 4.0 Hz | 1.5 | Emission oscillation rate |
| _EmissiveColor | Color HDR | -- | Infection Cyan (#00FFD1) | Vein glow color |
| _GrowthMask | Texture2D | -- | None | Per-character fungal growth pattern |
| _VeinMap | Texture2D | -- | None | Vein network UV map |
| _NoiseScale | Float | 1.0 - 10.0 | 4.0 | Perlin noise modulation scale |
| _GrowthOpacity | Float | 0.0 - 1.0 | 0.0 | GrowthMask blend factor |

### Tier Behavior

| Infection | PulseSpeed | GrowthOpacity | VeinMap Alpha | Particle Emit |
|-----------|-----------|---------------|--------------|--------------|
| 0-10 | Bypassed | 0.0 | 0.0 | No |
| 11-25 | 1.0 Hz | 0.0 | 0.3 | No |
| 26-50 | 1.5 Hz | 0.3 | 0.6 | No |
| 51-75 | 2.0 Hz | 0.5 | 0.8 | Yes (low rate) |
| 76-90 | 2.5 Hz | 0.8 | 0.9 | Yes (medium) |
| 91-100 | 3.0 Hz | 1.0 | 1.0 | Yes (high) |

### Visual Target

Veins glow from within the character sprite, pulsing like a heartbeat. At high Infection, the character appears to be more fungus than organism. Growth mask adds 3D-like fungal protrusions rendered in 2D sprite overlay.

## FossilMuralReveal Shader

SDF-based reveal shader for Fossil Murals. Player brushes across mural surface to reveal painted detail beneath stone.

### Parameters

| Parameter | Type | Range | Default | Description |
|-----------|------|-------|---------|-------------|
| _RevealTex | RenderTexture | 512x512 | Black | Per-mural reveal state |
| _BrushRadius | Float | 0.02 - 0.1 (UV) | 0.05 | Brush stamp size |
| _EdgeSoftness | Float | 0.005 - 0.05 | 0.02 | Soft falloff on reveal edge |
| _StoneColor | Color | -- | #8B7355 | Unrevealed surface tint |
| _MuralTex | Texture2D | -- | -- | Full painted mural artwork |
| _PaintSaturation | Float | 0.0 - 1.0 | 0.0 | Color fade-in after reveal |
| _GlowIntensity | Float | 0.0 - 1.0 | 0.3 | Bioluminescent edge highlight |
| _AutoFillThreshold | Float | 0.0 - 1.0 | 0.85 | Coverage % that triggers auto-complete |

### Reveal Pipeline

1. Player aim input produces a raycast hit with UV coordinates.
2. `CommandBuffer.Blit` stamps a soft circle onto `_RevealTex` at hit UV.
3. Shader samples `_RevealTex`: white = revealed, black = hidden.
4. Lerp between `_StoneColor` and `_MuralTex` based on reveal value.
5. Edge glow applied at reveal boundary using SDF gradient.
6. When coverage exceeds `_AutoFillThreshold`, remaining area floods in over 2 seconds.

## Bioluminescence Shader

Environment emissive shader for glowing fungal surfaces, veins, and atmospheric elements.

### Parameters

| Parameter | Type | Range | Default | Description |
|-----------|------|-------|---------|-------------|
| _GlowColor | Color HDR | -- | Zone-dependent | Base emission color |
| _PulseFrequency | Float | 0.1 - 2.0 Hz | 0.5 | Breathing rhythm |
| _PulseAmplitude | Float | 0.0 - 1.0 | 0.3 | Intensity variation range |
| _ProximityBoost | Float | 0.0 - 2.0 | 0.5 | Extra glow when player is near |
| _ProximityRadius | Float | 1.0 - 10.0 | 5.0 | Radius for proximity effect |
| _InfectionReactivity | Float | 0.0 - 1.0 | 0.2 | How much player Infection boosts glow |

### Zone Color Mapping

| Zone | GlowColor | PulseFrequency | Notes |
|------|-----------|---------------|-------|
| 1 Sporehaven | Infection Cyan | 0.3 Hz | Calm, slow breathing |
| 2 Cortex | Void Purple | 1.0 Hz | Synaptic firing pattern |
| 3 Ferment Pits | Corrode Orange | 0.5 Hz | Bubbling rhythm |
| 4 Chitinhall | Bloom Green | 0.4 Hz | Steady structural pulse |
| 5 Rot Garden | Bloom Green to Scorch Red | Variable | Bloom/decay cycle |
| 6 Myco-Forge | Scorch Red | 0.8 Hz | Forge heartbeat |
| 7 Necroveil | Void Purple (dim) | 0.2 Hz | Near-imperceptible |
| 8 Core | All colors | Matches boss phase | Reactive to gameplay |

## AcidPool Shader

Animated liquid surface for acid hazards in Ferment Pits and other zones.

### Parameters

| Parameter | Type | Range | Default | Description |
|-----------|------|-------|---------|-------------|
| _SurfaceColor | Color | -- | #A4C639 | Base acid color |
| _DepthColor | Color | -- | #2D5016 | Deep liquid color |
| _WaveSpeed | Float | 0.1 - 2.0 | 0.4 | Surface undulation rate |
| _WaveAmplitude | Float | 0.0 - 0.1 | 0.02 | Vertex displacement height |
| _BubbleRate | Float | 0.0 - 5.0 | 1.5 | Bubble particle spawn rate |
| _CausticsTex | Texture2D | -- | Procedural | Caustic light pattern |
| _CausticsSpeed | Float | 0.1 - 1.0 | 0.3 | Caustic animation speed |
| _DamageGlow | Float | 0.0 - 1.0 | 0.0 | Emissive flash on player contact |

### Performance Notes

- Vertex displacement limited to 4 sine wave octaves.
- Caustics computed in fragment shader using scrolling UV with two-layer blend.
- Bubble particles handled by VFX Graph (not shader) for GPU instancing.
- AcidPool shader skips vertex displacement on Low quality preset.

## DecayEntropy Shader

Dissolve shader for enemy death, environmental decay, and Infection Overload sequences.

### Parameters

| Parameter | Type | Range | Default | Description |
|-----------|------|-------|---------|-------------|
| _DissolveProgress | Float | 0.0 - 1.0 | 0.0 | Animated from 0 to 1 over death duration |
| _NoiseTex | Texture2D | -- | Simplex 256x256 | Dissolve pattern |
| _EdgeColor | Color HDR | -- | Infection Cyan | Dissolve edge emission |
| _EdgeWidth | Float | 0.01 - 0.1 | 0.03 | Width of emissive dissolve edge |
| _SporeEmitRate | Float | 0.0 - 100.0 | 20.0 | Spore particles at dissolve edge |
| _Direction | Vector2 | -- | (0, 1) | Dissolve sweep direction |

### Death Animation Integration

| Entity Type | Duration | Direction | Edge Color | Spore Rate |
|------------|----------|-----------|-----------|------------|
| Tier 1 enemy | 0.5s | Bottom-up | Infection Cyan | 10 |
| Tier 2 enemy | 0.8s | Center-out | Zone accent color | 30 |
| Tier 3 enemy | 1.2s | Radial | Necrospore Gold | 50 |
| Boss | 3.0s | Phase-dependent | Multi-color sweep | 100 |
| Player (Overload) | 2.0s | Inside-out | Infection Cyan | 80 |

## Performance Budget Summary

| Quality Preset | Shader Budget (Total) | Key Tradeoffs |
|---------------|----------------------|---------------|
| Low | 1.0ms | No vertex displacement, half-res glow, no proximity reactivity |
| Medium | 2.0ms | Full shaders, half-res bloom post-process |
| High | 3.0ms | Full shaders, full-res bloom, 2x GrowthMask resolution |
| Ultra | 4.0ms | All features, 4x GrowthMask, extended proximity radius |

## Implementation Notes

- All shaders authored in URP Shader Graph (`.shadergraph` assets in `Assets/_Project/Shaders/`)
- Shader variants stripped for target platform at build time via `ShaderVariantCollection`
- InfectionPulse parameters driven by `InfectionVisualSystem` reading `InfectionComponent` data
- DecayEntropy animated by `DeathAnimationSystem` via `MaterialPropertyBlock` (no material instances)
- Bioluminescence proximity uses `_WorldSpaceCameraPos` and per-object position uniform
- All HDR emission values clamped to 5.0 intensity max to prevent bloom artifacts
