# VFX Specification

## Overview

All VFX use Unity VFX Graph (GPU particles) for complex systems and the Particle System (CPU) for simple, low-count effects. Performance budget: max 10,000 active particles on screen at any time on medium quality.

## VFX Inventory

| Category | System Count | Max Particles (per instance) | GPU/CPU |
|----------|-------------|------------------------------|---------|
| Infection Glow | 5 (per tier) | 200 | GPU |
| Boon Activation | 7 (per civilization) | 300 | GPU |
| Fusion Merge | 1 | 500 | GPU |
| Death Explosion | 4 (per tier) | 150-400 | GPU |
| Boss Phase Transition | 8 (per boss) | 800 | GPU |
| Status Effects | 7 | 100 each | CPU |
| Environmental | 8 (per zone) | 500-2000 | GPU |

## Infection Glow VFX

Particles emitted from character sprite edges, scaling with Infection level.

| Tier | Particle Count | Size | Color | Behavior |
|------|---------------|------|-------|----------|
| 1 (11-25) | 10-20 | 2px | Infection Cyan, 30% alpha | Slow drift upward, 2s lifetime |
| 2 (26-50) | 30-50 | 3px | Infection Cyan, 50% alpha | Drift + slight orbit, 2.5s |
| 3 (51-75) | 60-100 | 4px | Cyan-to-green gradient | Active orbit, trail enabled, 3s |
| 4 (76-90) | 100-150 | 5px | Bright cyan, pulsing | Rapid orbit, longer trails, 3s |
| 5 (91-100) | 150-200 | 6px | White-core cyan | Frenzied motion, dense trails, 4s |

Emission source: sprite edge mask (SDF-derived). Particles inherit character velocity.

## Boon Activation VFX

Triggered when a player selects a boon. Each civilization has a themed activation burst.

| Civilization | Color | Shape | Duration | Special Element |
|-------------|-------|-------|----------|----------------|
| Rootborn | Bloom Green | Upward root tendrils | 1.5s | Ground cracks emit green light |
| Thinkers | Void Purple | Neural arc pattern | 1.0s | Electric sparks between nodes |
| Brewers | Corrode Orange | Bubbling eruption | 1.2s | Acid droplet splash on ground |
| Builders | Necrospore Gold | Hexagonal grid flash | 0.8s | Chitin plate assembly effect |
| Cultivators | Bloom Green + White | Petal bloom outward | 2.0s | Flower petals drift down after |
| Smiths | Scorch Red | Forge spark shower | 1.0s | Embers linger on ground 3s |
| Seekers | Void Purple + Cyan | Reality tear / glitch | 1.5s | Screen distortion (post-process) |

All boon VFX play centered on the player character. Audio synced to burst peak.

## Fusion Merge VFX

Played when two or more players initiate Fusion.

| Phase | Duration | Particles | Description |
|-------|----------|-----------|-------------|
| Approach | 0.5s | 100 | Mycelial tendrils reach from each player toward partner |
| Contact | 0.3s | 300 | Burst at midpoint, cyan shockwave ring |
| Merge | 0.8s | 500 | Spiraling inward vortex consuming both sprites |
| Emerge | 0.4s | 200 | Fused entity appears with radial burst |

Total duration: 2.0s. During merge, player sprites fade to silhouette (shader-driven). Fused entity sprite fades in during Emerge phase. Screen shake: 0.2s at Contact.

## Death Explosion VFX

| Entity Tier | Particle Count | Burst Duration | Elements |
|------------|---------------|----------------|----------|
| Tier 1 | 50 | 0.3s | Small spore puff, dissolve shader |
| Tier 2 | 150 | 0.5s | Medium burst, residual spore cloud (2s linger) |
| Tier 3 | 300 | 0.8s | Large explosion, screen shake (0.15s), ground impact ring |
| Boss | 400+ | 2.0s | Multi-phase: initial burst, sustained rain, final flash |

### Boss Death Phases

| Phase | Duration | Description |
|-------|----------|-------------|
| Initial burst | 0.5s | Bright flash at center, outward shockwave |
| Spore rain | 1.0s | Particles fall from burst point, zone-colored |
| Dissolve | 0.5s | DecayEntropy shader takes over, particles emit from dissolve edge |
| Loot burst | 0.3s | Gold particles mark loot drop locations |

## Boss Phase Transition VFX

Each boss has a unique phase transition effect triggered at HP thresholds.

| Boss | Trigger | VFX Description | Duration |
|------|---------|-----------------|----------|
| Spore Mother | 66%, 33% HP | Root eruptions from floor, screen tint green | 2.0s |
| The Architect | 50% HP | Arena geometry shifts, purple lightning arcs | 2.5s |
| The Distiller | 66%, 33% HP | Acid geysers erupt, orange fog fills arena | 1.5s |
| Hive Queen | 50% HP | Chitin plates reassemble, gold dust cloud | 2.0s |
| The Gardener | 75%, 50%, 25% HP | Bloom/wilt cycle, petals to rot to regrowth | 3.0s |
| The Smelter | 50% HP | Forge eruption, lava splash particles | 2.0s |
| Veil Warden | 66%, 33% HP | Reality fracture, void particles pour through tears | 2.5s |
| Mycelium Heart | 75%, 50%, 25% HP | Full-screen pulse, all zone colors cycle | 3.0s |

During phase transitions: enemies freeze, player has I-frames, boss is invulnerable.

## Status Effect VFX

Persistent particle effects attached to affected entities.

| Status | Color | Particles | Behavior |
|--------|-------|-----------|----------|
| Regeneration | Bloom Green | 15 | Gentle upward drift, leaf-like shapes |
| Clarity | Void Purple | 20 | Orbiting sparks around head |
| Resistance | Corrode Orange | 10 | Bubble shield surface shimmer |
| Fortify | Necrospore Gold | 12 | Hexagonal plate flickers around body |
| Bloom | Green + White | 20 | Petal particles circling feet |
| Temper | Scorch Red | 15 | Ember particles rising from hands |
| Insight | Cyan + Purple | 8 | Eye-like glow near character head |

All status VFX use CPU Particle System (low count, simple behavior). Disabled on Low quality.

## Environmental VFX (Per Zone)

| Zone | System | Particles | Behavior |
|------|--------|-----------|----------|
| 1 Sporehaven | Floating spores | 500 | Slow drift, soft glow, react to player movement |
| 2 Cortex | Synaptic arcs | 200 | Electric sparks between wall nodes, 0.5s bursts |
| 3 Ferment Pits | Acid drips + bubbles | 300 | Drip from ceiling, splash on surfaces, bubble columns |
| 4 Chitinhall | Chitin dust | 400 | Falling particles, accumulate on surfaces |
| 5 Rot Garden | Petals + spores | 600 | Mixed bloom petals and decay spores, wind-driven |
| 6 Myco-Forge | Embers + heat haze | 500 | Rising embers, heat distortion (post-process) |
| 7 Necroveil | Void motes | 200 | Near-invisible dark particles, occasional flash |
| 8 Core | Heartbeat pulses | 300 | Radial pulse waves synced to ambient audio |

Environmental VFX spawn within a volume around the camera. Particles beyond camera frustum are culled.

## Performance Budget

| Quality | Max Particles | VFX Graph Budget | CPU Particle Budget |
|---------|--------------|------------------|-------------------|
| Low | 3,000 | 1.0ms | 0.3ms |
| Medium | 10,000 | 2.0ms | 0.5ms |
| High | 20,000 | 3.0ms | 0.5ms |
| Ultra | 30,000 | 4.0ms | 0.5ms |

### LOD Rules

| Distance | Particle Reduction | Trail Disable |
|----------|-------------------|--------------|
| < 10 units | Full quality | No |
| 10-20 units | 50% spawn rate | Yes |
| > 20 units | 25% spawn rate | Yes |
| Off-screen | Culled entirely | N/A |

## Implementation Notes

- VFX Graph assets stored in `Assets/_Project/VFX/` organized by category
- Environmental VFX use shared base graph with per-zone property overrides (ScriptableObject)
- Infection Glow driven by `InfectionVFXSystem` reading `InfectionComponent` and updating VFX Graph exposed properties
- Death VFX instantiated from pool by `DeathVFXSystem` on `EntityDied` event
- Boss phase VFX pre-loaded as Addressable assets on boss arena entry
- Status VFX attached to entity via `StatusVFXComponent` referencing pooled ParticleSystem instances
- Screen shake triggered by `ScreenShakeSystem` consuming `ScreenShakeEvent` (amplitude, duration, falloff)
