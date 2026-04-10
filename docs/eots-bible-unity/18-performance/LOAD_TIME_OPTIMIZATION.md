# Load Time Optimization

## Load Time Budgets

| Transition | Budget | Target | Method |
|-----------|--------|--------|--------|
| Cold boot to main menu | < 5.0s | 3.0s | Minimal scene + async preload |
| Main menu to Canopy hub | < 3.0s | 2.0s | Async scene load + Addressables |
| Hub to delve (zone entry) | < 2.0s | 1.5s | Proc-gen + room prefab instantiate |
| Room-to-room transition | < 0.5s | 0.1s | Pre-loaded adjacent rooms |
| Zone-to-zone transition | < 1.0s | 0.5s | Async tileset swap |
| Death/Overload respawn | Instant | 0ms | No scene change |
| Return to hub from delve | < 2.0s | 1.0s | Hub always warm-loaded |
| Quit to main menu | < 1.0s | 0.5s | Unload scene, keep base |

## Boot Sequence Optimization

```
Frame 0-10 (~160ms):
  Unity splash (mandatory, optimized duration)
  
Frame 10-30 (~330ms):
  Load Master FMOD bank (2 MB)
  Initialize custom ECS (memory allocation)
  Initialize network subsystem
  
Frame 30-60 (~500ms):
  Load main menu scene (minimal: camera + canvas)
  Load UI atlas (2 MB)
  Start async preload of hub scene in background
  
Frame 60+:
  Menu is interactive
  Background: preload Canopy scene + SFX banks
```

## Addressables Loading Strategy

| Group | Load Timing | Unload Timing | Size |
|-------|------------|---------------|------|
| Core | Boot (blocking) | Never | 15 MB |
| UI | Boot (blocking) | Never | 5 MB |
| Hub | Boot (async, background) | Never | 25 MB |
| Zone_{N}_Tiles | Zone entry (async) | Zone exit | 8-15 MB |
| Zone_{N}_Enemies | Zone entry (async) | Zone exit | 5-10 MB |
| Boss_{N} | Boss room entry (async) | Boss death | 5-8 MB |
| Narrator | On demand (stream) | After playback | Per-line |

## Room Pre-Loading Pipeline

```
Current Room: Active (rendering + simulation)
  |
  +-- Door North → Room N: Pre-loaded (inactive, ready to activate in 1 frame)
  +-- Door South → Room S: Pre-loaded
  +-- Door East  → Room E: Pre-loaded
  +-- Door West  → Room W: Pre-loaded

On Transition Through Door:
  1. Activate target room (< 1ms: already loaded)
  2. Deactivate current room (< 1ms)
  3. Identify new adjacent rooms
  4. Async load new adjacent rooms (~50ms each, non-blocking)
  5. Unload rooms no longer adjacent (immediate, GC later)
```

## Async Loading Rules

| Rule | Rationale |
|------|-----------|
| Never block main thread for asset loading | Prevents frame drops |
| Use `Addressables.LoadAssetAsync<T>()` | Non-blocking |
| Chain with `.Completed` callback | Process when ready |
| Show loading indicator if load > 500ms | Player feedback |
| Cancel pending loads on scene unload | Prevent stale callbacks |
| Priority: room prefabs > textures > audio | Gameplay first |

## Scene Loading Configuration

```csharp
public async Task LoadZone(int zoneId)
{
    // Show loading screen
    _loadingUI.Show();
    
    // Phase 1: Load zone scene additively (contains tilemaps, lighting)
    var sceneOp = SceneManager.LoadSceneAsync(
        $"Zone_{zoneId}", LoadSceneMode.Additive);
    sceneOp.allowSceneActivation = false;
    
    // Phase 2: Load zone-specific Addressables in parallel
    var tilesetOp = Addressables.LoadAssetAsync<SpriteAtlas>($"Zone_{zoneId}_Tiles");
    var enemyOp = Addressables.LoadAssetAsync<GameObject>($"Zone_{zoneId}_Enemies");
    var audioOp = FMODUnity.RuntimeManager.LoadBank($"SFX_Zone_{zoneId}");
    
    await Task.WhenAll(tilesetOp.Task, enemyOp.Task);
    
    // Phase 3: Generate rooms via Rust proc-gen
    var zoneGraph = ProcGenBridge.GenerateZone(zoneId, _currentSeed);
    
    // Phase 4: Activate scene
    sceneOp.allowSceneActivation = true;
    await sceneOp;
    
    // Phase 5: Instantiate starting room
    InstantiateRoom(zoneGraph.StartRoom);
    PreLoadAdjacentRooms(zoneGraph.StartRoom);
    
    _loadingUI.Hide();
}
```

## Loading Screen Design

| Duration | Display |
|----------|---------|
| < 500ms | No loading screen (seamless) |
| 500ms - 2s | Minimal: zone name + progress bar |
| 2s - 5s | Full: zone art + lore tip + progress bar |
| > 5s | Should never happen (P1 bug if it does) |

## Memory During Loading

| Phase | Memory Spike | Notes |
|-------|-------------|-------|
| Scene load | +50 MB temporary | Unity scene deserialization |
| Addressable load | +20 MB temporary | Asset decompression |
| Proc-gen | +5 MB temporary | Zone graph computation |
| Room instantiate | +10 MB temporary | Prefab instantiation |
| Post-load GC | -30 MB (freed temp) | Incremental GC cleans up |
| **Max spike** | **~85 MB above steady** | Within budget |

## Platform-Specific Load Times

| Platform | Boot→Menu | Menu→Hub | Hub→Delve | Room Transition |
|----------|----------|---------|----------|----------------|
| PC (NVMe SSD) | 2.0s | 1.5s | 1.0s | < 50ms |
| PC (SATA SSD) | 3.0s | 2.0s | 1.5s | < 100ms |
| PC (HDD) | 5.0s | 3.5s | 2.5s | < 200ms |
| PS5 (SSD) | 2.0s | 1.0s | 0.8s | < 50ms |
| Xbox Series X (SSD) | 2.0s | 1.0s | 0.8s | < 50ms |
| Xbox Series S (SSD) | 2.5s | 1.5s | 1.0s | < 50ms |
| Switch 2 | 3.5s | 2.5s | 2.0s | < 100ms |
| Steam Deck | 3.0s | 2.0s | 1.5s | < 100ms |

## Optimization Techniques Applied

| Technique | Impact | Where Applied |
|-----------|--------|---------------|
| Async scene loading | Non-blocking transitions | All scene loads |
| Addressable pre-warming | Reduce first-access latency | Hub, core assets |
| Room pre-loading | Near-instant room transitions | Adjacent rooms |
| Asset bundle compression | Faster disk reads (LZ4) | All bundles |
| Texture streaming | Reduce initial load | Large textures (parallax) |
| Object pooling | Avoid instantiation cost | Enemies, projectiles, VFX |
| Shader warmup | Prevent first-frame stutter | PreloadShaders on boot |
| IL2CPP AOT | Avoid JIT pauses | All platforms |
