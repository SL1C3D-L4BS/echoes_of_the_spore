# Adaptive Music System

## Architecture

FMOD Studio drives all adaptive music via labeled parameters on each zone event.
Each zone has a single FMOD Event containing all layers; parameters control transitions.

## Zone Layer Stack

| Layer | FMOD Parameter | Range | Default |
|-------|---------------|-------|---------|
| Base | `MusicState` | 0.0 | Always playing |
| Exploration | `MusicState` | 0.0-0.3 | Subtle variation of base |
| Combat | `MusicState` | 0.4-0.7 | Combat intensity |
| Boss | `MusicState` | 0.8-1.0 | Boss encounter |
| Fusion Overlay | `FusionActive` | 0.0/1.0 | Layered on top during fusion |

## Transition Rules

| From | To | Trigger | Fade Duration | Method |
|------|----|---------|---------------|--------|
| Base | Combat | Enemy enters alert state | 2.0s | Crossfade |
| Combat | Base | Last enemy dead + 5.0s cooldown | 3.0s | Crossfade |
| Base/Combat | Boss | Boss room door closes | 0.5s | Stinger → hard cut |
| Boss Phase 1 | Phase 2 | Boss HP < 50% | 1.0s | Musical bridge |
| Boss Phase 2 | Phase 3 | Boss HP < 25% | 0.5s | Stinger → hard cut |
| Any | Fusion Overlay | Fusion merge starts | 1.5s | Additive layer |
| Boss | Victory | Boss HP = 0 | 0.0s | Stinger, then fade to base |

## Hub Music (Canopy) Evolution

The Canopy hub theme evolves with Community Tree growth.

| Tree Stage | Variation | Instruments Added | BPM |
|------------|-----------|-------------------|-----|
| Stage 0 (bare) | Sparse, lonely | Solo acoustic guitar | 70 |
| Stage 1 (sprout) | Hopeful | + Celeste, light pad | 75 |
| Stage 2 (sapling) | Growing warmth | + Strings quartet | 80 |
| Stage 3 (mature) | Full, vibrant | + Woodwinds, harp | 85 |
| Stage 4 (ancient) | Majestic, complete | Full ensemble + choir | 90 |

FMOD Parameter: `TreeStage` (int 0-4), set by `CommunityTreeManager.OnStageChanged()`.

## Combat Intensity Scaling

```
CombatIntensity = clamp(
    (active_enemies * 0.15) + (player_hp_missing * 0.3) + (combo_count * 0.01),
    0.0, 1.0
)
```

| Intensity Range | Musical Response |
|----------------|------------------|
| 0.0 - 0.2 | Base + light percussion |
| 0.2 - 0.5 | Add bass line, increase tempo by 5% |
| 0.5 - 0.8 | Full combat instrumentation, distortion layer |
| 0.8 - 1.0 | Maximum intensity, staccato strings, heavy percussion |

## Infection Influence on Music

| Infection Tier | Music Modifier |
|---------------|----------------|
| 0-25 | No change |
| 26-50 | Detuned overtones added (+5 cents random) |
| 51-75 | Minor → diminished chord substitution |
| 76-90 | Melodic instruments replaced with tonal drones |
| 91-100 | All music replaced with spore ambience soundscape |

FMOD Parameter: `InfectionLevel` (float 0.0-1.0), updated every 500ms from Redis sync.

## Spore Plan Music

When a Spore Plan is activated, a unique musical moment plays:

| Phase | Duration | Audio |
|-------|----------|-------|
| Charge-up | 1.5s | Ascending glissando, all ambient muted |
| Execution | Variable | Unique per plan type (see SFX_DESIGN.md) |
| Aftermath | 2.0s | Reverb tail, slow fade back to zone music |

## Memory and Streaming

| Asset Type | Loaded | Size |
|-----------|--------|------|
| Zone music event | On zone entry | 8-15 MB per zone |
| Hub variations | Always loaded | 20 MB total |
| Combat layers | With zone event | Included in zone size |
| Boss themes | On boss room entry | 5-8 MB per boss |
| Stingers | Always loaded | 2 MB total |
| **Max concurrent** | — | **~45 MB music** |
