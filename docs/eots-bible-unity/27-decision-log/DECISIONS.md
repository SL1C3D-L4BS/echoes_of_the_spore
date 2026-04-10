# 27 — DECISION LOG

| ID | Decision | Alternatives | Rationale | Date |
|---|---|---|---|---|
| D01 | **Unity 6.3 LTS** | Godot 4.6, Unreal 5.5, Custom | Unity: proven for 2D MVs (Hollow Knight, Silksong, Dead Cells), C# (Claude Code fluent), built-in console export, MCP connection verified, massive ecosystem. Godot: lighter but less proven console pipeline. Unreal: 5% royalty, 3D-focused. | Apr 2026 |
| D02 | **Custom C# ECS** (not Unity DOTS) | Unity DOTS, pure MonoBehaviour | DOTS uses float (not deterministic). MonoBehaviours too slow for 4096 entities at 60Hz. Custom ECS in C# is ~2,000 lines, guarantees FixedPoint64 determinism, and has zero Unity deps in core. | Apr 2026 |
| D03 | **FixedPoint64 (32.32 long)** in C# | float, double, decimal, 16.16 | float non-deterministic cross-platform. double overkill. decimal too slow. 16.16 insufficient range. C# long provides identical 32.32 fixed-point to C++ int64_t. | Apr 2026 |
| D04 | **URP 2D Renderer** (not HDRP, not Built-in) | HDRP, Built-in | URP 2D: purpose-built for 2D, Shader Graph support, 2D Lights, lightweight. HDRP overkill. Built-in deprecated path. | Apr 2026 |
| D05 | **IL2CPP** scripting backend | Mono | IL2CPP: required for console, better runtime perf, smaller builds. Mono: faster iteration but not shippable to consoles. Use Mono in Editor, IL2CPP for builds. | Apr 2026 |
| D06 | **Unity Input System** (not legacy) | Legacy InputManager | Modern, action-based, rebindable at runtime, supports simultaneous KB+M and gamepad. | Apr 2026 |
| D07 | **Rust for proc-gen** via NativePlugin | C# implementation, C++ plugin | Rust: memory-safe, no_std WASM, C FFI, deterministic. C# would work but Rust is faster for graph algorithms. Unity NativePlugin system handles .so/.dll loading. | Apr 2026 |
| D08 | **FMOD for Unity** (not Unity native audio) | Unity AudioSource + AudioMixer | FMOD: industry standard, adaptive music, real-time DSP, event-based architecture. Unity audio lacks advanced DSP for Spore-Talk. | Apr 2026 |
| D09 | **ScriptableObjects** for game data | JSON, XML, custom binary | SOs: Unity-native, Inspector-editable, hot-reloadable, version-controlled as YAML. Designers edit without code. | Apr 2026 |
| D10 | **Addressables** for room loading | Resources.Load, direct references | Addressables: async loading, memory management, future CDN delivery for DLC. | Apr 2026 |
| D11 | **Headless Unity server** (initially) | Standalone C# console, Photon, PlayFab | Same codebase as client. ECS is pure C# — can extract to standalone later. | Apr 2026 |
| D12 | **60Hz fixed tick** | 30Hz, 120Hz | Standard for action games. Matches common display refresh. | Apr 2026 |
| D13 | **Rollback netcode** (custom) | Unity Netcode for GO, Fish-Net lockstep | Unity NGO is server-authoritative only. Fish-Net doesn't have rollback. Custom rollback is required for action combat feel. | Apr 2026 |
