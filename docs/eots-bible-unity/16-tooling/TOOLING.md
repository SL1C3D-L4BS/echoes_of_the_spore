# 16 — TOOLING, BUILD SYSTEM & DEVELOPER WORKFLOW

## Build Targets
| Target | Method | Purpose |
|---|---|---|
| Unity Editor (Linux/Win/Mac) | Unity Hub | Level design, scene editing, playtesting |
| Client (Win x64) | Unity Build | Primary player build |
| Client (Linux x64) | Unity Build | Steam Deck / Linux |
| Client (macOS) | Unity Build | Secondary |
| Client (PS5) | Unity Build + Sony SDK | Console |
| Client (Xbox Series) | Unity Build + GDK | Console |
| Client (Switch 2) | Unity Build + Nintendo SDK | Console |
| Dedicated Server | Unity Server Build (headless) | Authoritative server |
| Rust Proc-Gen (native) | cargo build --release | .so/.dll for Unity NativePlugin |
| Rust Proc-Gen (WASM) | wasm-pack build | Web level visualizer |
| Session Service (Rust) | cargo build --release | gRPC Redis wrapper |

## CI/CD Pipeline
1. Push to main → GitHub Actions
2. C# compile + Roslyn analysis (~1 min)
3. Unity Test Runner: EditMode tests (ECS unit tests, math tests) (~2 min)
4. Unity Test Runner: PlayMode tests (integration) (~3 min)
5. Rust compile + unit tests + 1000-zone validation (~3 min)
6. Unity batch build: Linux client (~5 min)
7. Deploy headless server to staging
8. On tagged release: build all platforms via Unity Build Server or Cloud Build

**Total CI time: ~15 minutes**

## Custom Editor Tools (Unity Editor Scripts)
| Tool | Location | Purpose |
|---|---|---|
| Zone Visualizer | Editor/ZoneVisualizer.cs | EditorWindow showing proc-gen room graph |
| Room Template Editor | Editor/RoomTemplateEditor.cs | Custom inspector for room prefab Tilemaps |
| Boon Balance Inspector | Editor/BoonBalanceWindow.cs | View all boons, rarities, damage values in one window |
| Infection Curve Preview | Editor/InfectionCurve.cs | AnimationCurve editor showing infection progression |
| ECS Entity Inspector | Editor/ECSInspector.cs | In-Play-mode view of live ECS entity state |
| Replay Viewer | Editor/ReplayViewer.cs | Load saved replays, step through ticks, detect desyncs |

## Source Control
- Git + LFS for Textures/, Audio/, Prefabs/ (binary Unity assets)
- .gitignore: standard Unity (Library/, Temp/, Logs/, obj/, Builds/)
- Branch strategy: main → develop → feature/* branches
- Merge requires: CI green + 1 code review
- Unity YAML merge: use UnityYAMLMerge tool for .unity/.prefab merge conflicts
- Smart merge registered in .gitconfig for scene/prefab files
