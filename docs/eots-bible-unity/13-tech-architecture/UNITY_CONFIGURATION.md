# Unity Project Configuration

## Required Packages (manifest.json)

| Package | Version | Purpose |
|---------|---------|---------|
| Universal RP | 17.x | 2D Renderer, Shader Graph, 2D Lights |
| Input System | 1.11+ | Action-based input, rebindable |
| Cinemachine | 3.x | 2D camera follow, screen shake |
| TextMeshPro | 3.x | UI text rendering |
| Unity Test Framework | 1.x | EditMode + PlayMode tests |
| 2D Sprite | 1.x | Sprite Atlas, 9-slice |
| 2D Tilemap | 1.x | Room tile rendering |
| 2D Tilemap Extras | 2.x | Rule Tiles, animated tiles |
| Addressables | 2.x | Optional: async asset loading |

## Project Settings

### Physics 2D
```
Gravity: (0, 0)           // Disabled — our ECS handles gravity
Auto Simulation: OFF       // We do NOT use Unity physics
Layer Collision Matrix: all OFF
```

### Time
```
Fixed Timestep: 0.01666667  // 60 Hz (1/60)
Maximum Allowed Timestep: 0.1
```

### Quality
```
Single quality level (2D, no LOD)
VSync: OFF (we manage frame timing)
Target Frame Rate: -1 (uncapped, let display handle)
```

### Graphics
```
Color Space: Linear
Graphics APIs: Vulkan, OpenGLES3 (fallback)
Rendering Path: Forward (URP 2D)
```

### Player
```
Default Is Full Screen: true
Resolution Dialog: disabled
Run In Background: true (multiplayer)
```

### Script Execution Order
```
SimulationRunner: -100    // Ticks first
InputBridge: -90          // Gathers input before sim
EntitySpawner: -80        // Spawns before rendering
RenderProxy: 0            // Default (renders after sim)
AudioBridge: 100          // After everything
```

### URP 2D Renderer Settings
```
2D Lights: enabled
Global Light: ambient (0.15 intensity, warm white)
Point Light 2D: used for bioluminescence
Freeform Light 2D: used for zone-specific effects
Bloom: enabled (infection glow)
Vignette: enabled (damage feedback)
Pixel Perfect Camera: enabled on main camera
Sprite sorting: Y-axis (standard 2D depth)
```

## Assembly Definitions

| Assembly | References | Contains |
|----------|-----------|----------|
| EotS.Core | (none) | ECS, Math, Netcode, Systems, Components |
| EotS.Bridge | EotS.Core, UnityEngine | SimulationRunner, RenderProxy, InputBridge |
| EotS.UI | EotS.Core, EotS.Bridge, TMPro | HUD, Menus, Overlays |
| EotS.Audio | EotS.Core, EotS.Bridge, FMODUnity | AudioManager, SporeTalkDSP |
| EotS.ProcGen | EotS.Core | ProcGenBridge, ZoneGraphData |
| EotS.Backend | EotS.Core | BackendClient, CommunityTreeSync |
| EotS.Tests.Edit | EotS.Core, NUnit | Unit tests (Core only) |
| EotS.Tests.Play | EotS.Core, EotS.Bridge, NUnit | Integration tests |
