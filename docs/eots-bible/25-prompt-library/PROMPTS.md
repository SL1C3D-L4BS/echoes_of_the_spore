# Echoes of the Spore -- Prompt Library

> Production-grade XML agent prompts for Claude Opus 4.6.
> Each prompt is a self-contained template: fill in the `{{PLACEHOLDER}}` parameters, paste the XML block into Claude, and receive compilable, test-covered output.

---

## How to Use

1. Open the prompt file you need.
2. Copy the entire `<agent-prompt>` XML block.
3. Replace every `{{PLACEHOLDER}}` with your concrete values.
4. Paste into Claude (Opus 4.6 recommended).
5. Review generated code against the Quality Gates listed in the prompt.

---

## Prompt Index

| # | File | Purpose |
|---|------|---------|
| 1 | [prompt-ecs-component.md](prompt-ecs-component.md) | Create a new ECS component (C# struct, FixedPoint64, zero Unity API) |
| 2 | [prompt-ecs-system.md](prompt-ecs-system.md) | Create a new ECS system with deterministic update loop |
| 3 | [prompt-enemy-type.md](prompt-enemy-type.md) | Design a new enemy type with behavior tree and stat block |
| 4 | [prompt-boss-fight.md](prompt-boss-fight.md) | Design a 3-phase boss encounter with mechanics and loot table |
| 5 | [prompt-echo-boon.md](prompt-echo-boon.md) | Create an Echo Boon (civilization-themed ability with upgrade tiers) |
| 6 | [prompt-zone-design.md](prompt-zone-design.md) | Design a complete zone: biome, hazards, enemy roster, boss |
| 7 | [prompt-fused-class.md](prompt-fused-class.md) | Create a Fused Class combination with unique kit |
| 8 | [prompt-networking-system.md](prompt-networking-system.md) | Build a networking feature with rollback compatibility |
| 9 | [prompt-procgen-module.md](prompt-procgen-module.md) | Build a Rust proc-gen module with C FFI for Unity interop |

---

## Conventions

- **FixedPoint64 everywhere.** No `float` or `double` in simulation code.
- **Zero Unity API in ECS components/systems.** Pure C# structs and logic.
- **Deterministic.** Every system must produce identical output given identical input across all platforms.
- **Test-covered.** Every prompt requires unit tests alongside production code.
- **Bible-anchored.** Each prompt references the relevant section of the EotS Bible for design context.

---

## Bible Cross-References

| Bible Section | Relevant Prompts |
|---------------|-----------------|
| `13-tech-architecture/TECH_ARCH.md` | ECS Component, ECS System, Networking, ProcGen |
| `05-enemies-bosses/ENEMIES.md` | Enemy Type, Boss Fight |
| `06-echo-boons/ECHO_BOONS.md` | Echo Boon |
| `07-zones/ZONES.md` | Zone Design |
| `04-classes/CLASSES.md` | Fused Class |
| `14-networking/NETWORKING.md` | Networking System |
| `15-procgen/PROCGEN.md` | ProcGen Module |
