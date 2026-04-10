# 07 — MULTIPLAYER, FUSION & SOCIAL SYSTEMS

## Session Architecture

- Max players per session: 4
- Sessions are instanced per-delve (not persistent world instances)
- Matchmaking via Redis sorted sets (FIFO by queue time)
- Cross-play: PC + Console. Cloud-mobile is spectate-only.
- Session lifecycle: Lobby → Active → Boss → Complete/Failed → Disband

## Fungal Fusion — Detailed Specification

### Fusion Lifecycle State Machine

```
NONE → REQUESTING → MERGING → FUSED → SEPARATING → NONE
```

- NONE → REQUESTING: Player sends fusion_request=true AND is within 3.0 units of another player who also sent fusion_request=true on the same tick.
- REQUESTING → MERGING: All requesting players (2-4) have been in Requesting for 30 ticks (500ms) and remain within proximity. fusion_timer = 60 ticks (1s merge animation).
- MERGING → FUSED: fusion_timer reaches 0. Fused entity created. Original entities become FusionGhost (exist for netcode, not rendered/simulated for physics).
- FUSED → SEPARATING: Pilot sends fusion_request=true (toggle). fusion_timer = 30 ticks (500ms).
- SEPARATING → NONE: Timer reaches 0. Fused entity destroyed. Originals restored. HP split proportionally.

### Fused Entity Stats

```
Fused HP = sum(partner HP) × 1.15 (15% Fusion Bonus, floor)
Fused MaxHP = sum(partner MaxHP) × 1.15
Fused Position = centroid(partner positions)
Fused Collider = AABB_union(partner colliders) × 1.10
Fused Infection = floor(mean(partner infections))
```

### Dual Brain Control Scheme

| Players | Pilot | Gunner | Support | Relay |
|---|---|---|---|---|
| 2P | Movement + dodge | Aim + abilities | — | — |
| 3P | Movement | Aim + offensive abilities | Defensive abilities + buffs | — |
| 4P | Movement | Aim + primary attack | Defensive + crowd control | Support abilities + resource management + Spore-Talk relay |

### Spore Plan (Cooperative Turn())

- Activation: requires full Spore Gauge (fills over ~60s of combat)
- Effect: time slows to 10% (sim rate drops to 6Hz) for 4 seconds real-time
- Planning budget: 100 points (movement costs 1/unit, abilities cost cooldown÷10)
- Each Fusion role plots their contribution simultaneously
- Execution: all queued actions play out in 2 seconds of real-time
- Recovery: 5-second global cooldown on all abilities post-execution
- Solo version: Micro-Plan (2 seconds slow-time, 50 points, movement + attack only)

## Rite of Echoes (Scheduled PvP)

- Frequency: every 72 hours (real-time)
- Format: 4v4 (or 2 Fused entities per team)
- Arena: symmetrical, procedurally generated
- Objective: capture and deposit Echo fragments into central Spore Heart. First to 7 points.
- Duration: ~10 minutes per match
- Winner reward: 500 Echo bonus to chosen Community Tree branch
- Loser penalty: +10 Infection (unpurgeable for 24 hours)
- 3 consecutive losses: "Consumed" title (visible, removed on next win)
- Spectator mode: elevated platforms, Mycelium narration

## Spore-Talk (Proximity VOIP)

- Proximity falloff: 100% volume at 0-5 units, linear falloff to 0% at 20+ units
- Infection DSP chain: Pitch Shift → Resonance Filter → Word Replacement → Spore Mix
- Fusion override: fused partners hear each other at 100% regardless of proximity
- External listeners hear fused entity's blended voice at fused entity's position
- Push-to-talk default, open-mic configurable
- Clip Mode: save last 30s of raw + processed audio on command
- Infection Replay: post-run, re-process raw audio at any infection level
