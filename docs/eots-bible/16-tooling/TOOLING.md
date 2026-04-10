# 16 — TOOLING, BUILD SYSTEM & DEVELOPER WORKFLOW

## Build Targets
| Target | Configuration | Purpose |
|---|---|---|
| Editor (Win64) | Development | Level design, asset import, playtesting |
| Client (Win64) | Development/Shipping | PC player build |
| Client (PS5) | Shipping | Console build (Month 17+) |
| Client (XSX) | Shipping | Console build (Month 17+) |
| Dedicated Server (Linux x86_64) | Shipping | Authoritative game server |
| Rust Proc-Gen (native) | Release | C FFI library for UE5 |
| Rust Proc-Gen (WASM) | Release | Web tools, level visualizer |
| Session Service (Rust) | Release | gRPC Redis wrapper |

## CI/CD Pipeline
1. Push to main → GitHub Actions triggers
2. C++ compile (ECS standalone) + unit tests (~2 min)
3. Rust compile (all targets) + unit tests + 1000-zone gen validation (~5 min)
4. UE5 Editor compile (Win64) (~15 min)
5. ECS integration tests (determinism verification) (~3 min)
6. Package client (Win64 Development) (~20 min)
7. Deploy to staging server for smoke tests
8. On tagged release: build Shipping for all platforms

## Custom Tools
| Tool | Technology | Purpose |
|---|---|---|
| Zone Visualizer | React + WASM proc-gen | Web-based room graph preview |
| Tilemap Editor | UE5 Editor plugin | Author room templates |
| Boon Balance Sheet | Google Sheets + script | Designer-facing balance spreadsheet → JSON export |
| Infection Curve Simulator | Python + matplotlib | Plot infection accumulation over simulated runs |
| Replay Analyzer | Custom C++ CLI | Load replay files, verify determinism, detect desyncs |

## Source Control
- Git with LFS for binary assets (textures, audio, FMOD banks)
- Branch strategy: main (stable) + develop (integration) + feature/* branches
- Merge requires: CI green + 1 code review
- Art assets: separate LFS repo linked via submodule (keeps main repo lean)
