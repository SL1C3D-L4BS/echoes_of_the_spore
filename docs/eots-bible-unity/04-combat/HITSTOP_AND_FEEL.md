# Hitstop & Game Feel Specification

## Design Philosophy

Every input must feel immediate and every hit must feel impactful. Frame data is law. All values are tuned at 60 FPS (16.67 ms per frame). Deterministic simulation runs at 60 ticks/second regardless of render framerate.

## Input Latency Budget

| Stage | Budget | Notes |
|-------|--------|-------|
| Hardware polling | 1 frame (16.7 ms) | Unity InputSystem, polled each FixedUpdate |
| Input processing | 0 frames | Same tick as poll |
| State change | 0 frames | Applied immediately to ECS |
| Visual response | 1 frame (16.7 ms) | Next render frame |
| **Total** | **< 3 frames (50 ms)** | Target: indistinguishable from instant |

## Attack Frame Data

| Attack | Startup | Active | Recovery | Total | Cancel Window |
|--------|---------|--------|----------|-------|---------------|
| Light 1 | 4f (66 ms) | 3f (50 ms) | 8f (133 ms) | 15f (250 ms) | Recovery f5+ into Light 2 |
| Light 2 | 4f (66 ms) | 3f (50 ms) | 8f (133 ms) | 15f (250 ms) | Recovery f5+ into Light 3 |
| Light 3 | 6f (100 ms) | 4f (66 ms) | 10f (166 ms) | 20f (333 ms) | No cancel (full commitment) |
| Heavy | 10f (166 ms) | 5f (83 ms) | 16f (266 ms) | 31f (516 ms) | No cancel |
| Dash Attack | 0f (instant) | 4f (66 ms) | 10f (166 ms) | 14f (233 ms) | Recovery f6+ into Light 1 |
| Air Light | 4f (66 ms) | 3f (50 ms) | 6f (100 ms) | 13f (216 ms) | Recovery f4+ into Air Heavy |
| Air Heavy | 8f (133 ms) | 5f (83 ms) | 14f (233 ms) | 27f (450 ms) | No cancel |

## Hitstop Specification

| Event | Hitstop Duration | Shake Intensity | Notes |
|-------|-----------------|-----------------|-------|
| Light hit (on enemy) | 3 frames (50 ms) | 0.5 px | Snappy feedback |
| Heavy hit (on enemy) | 5 frames (83 ms) | 2.0 px | Weighty impact |
| Critical hit | 7 frames (116 ms) | 3.0 px | Emphasize crit moment |
| Player takes hit | 5 frames (83 ms) | 1.5 px | Danger signal |
| Boss phase transition | 12 frames (200 ms) | 4.0 px | Dramatic pause |
| Shatter detonation | 8 frames (133 ms) | 5.0 px | AoE payoff |
| Overload trigger | 15 frames (250 ms) | 0 px (white flash instead) | Gravity of the moment |

### Hitstop Implementation

```
During hitstop:
  - Attacker animation pauses at contact frame
  - Defender animation pauses at hit-react frame
  - Projectiles pause (if applicable)
  - Particles continue (visual feedback preserved)
  - Input buffer continues accepting inputs
  - Simulation tick counter pauses (deterministic)
```

## Knockback Distances

| Attack Type | Knockback (tiles) | Direction | Vertical Component |
|-------------|-------------------|-----------|-------------------|
| Light hit | 1.5 | Away from attacker | None |
| Heavy hit | 3.0 | Away from attacker | None |
| Dash attack | 2.0 | Attacker's facing | None |
| Air heavy (downward) | 0 (horizontal) | N/A | Bounce: 2 tiles up |
| Shatter explosion | 4.0 | Radial from center | 1 tile up |
| Boss slam | 5.0 | Radial | 2 tiles up |

## Invincibility Frames

| Source | i-frames | Duration | Notes |
|--------|----------|----------|-------|
| Dash | 6 frames | 100 ms | Frames 1-6 of dash |
| Player hit (damage taken) | 20 frames | 333 ms | Generous for co-op readability |
| Overload recovery | 60 frames | 1000 ms | Full second of safety post-overload |
| Fusion merge animation | 30 frames | 500 ms | Cannot be hit during merge |
| Fusion unmerge | 15 frames | 250 ms | Brief protection on split |
| Room entry | 12 frames | 200 ms | Prevents spawn-camping |

## Input Buffering

| Buffer Type | Window | Behavior |
|-------------|--------|----------|
| Jump buffer | 6 frames (100 ms) | Jump input stored while airborne/in recovery |
| Coyote time | 6 frames (100 ms) | Jump still valid after leaving platform edge |
| Attack buffer | 4 frames (66 ms) | Attack stored during recovery of previous attack |
| Dash buffer | 4 frames (66 ms) | Dash stored during attack recovery |
| Heal buffer | 0 frames | No buffer; must be deliberate (prevents accidental heals) |

## Movement Feel Parameters

| Parameter | Value | Notes |
|-----------|-------|-------|
| Run speed | 4.5 FP64 units/tick | Responsive ground movement |
| Dash speed | 12.0 FP64 units/tick | ~2.7x run speed |
| Dash distance | 4.0 tiles | Fixed distance |
| Dash duration | 8 frames (133 ms) | Quick traversal |
| Dash startup | 0 frames (instant) | Zero-delay response |
| Dash cooldown | 18 frames (300 ms) | From dash end to next dash |
| Jump force | 12.0 FP64 units/tick | ~3.5 tile max height |
| Gravity | 0.6 FP64 units/tick^2 | Snappy arc feel |
| Max fall speed | 12.0 FP64 units/tick | Terminal velocity cap |
| Wall slide speed | 2.0 FP64 units/tick | Slow descent on walls |
| Wall jump force | 10.0 horizontal, 10.0 vertical | Angled away from wall |

## Camera Feel

| Event | Camera Behavior | Duration |
|-------|----------------|----------|
| Player hit | Micro-shake (direction of hit) | 3 frames |
| Heavy attack land | Forward push (2 px toward target) | 5 frames |
| Combo tier up | Brief zoom pulse (2% in, 2% out) | 8 frames |
| Boss phase transition | Slow zoom to boss (10%), hold | 1 second |
| Overload | Rapid zoom out (5%) + return | 0.5 seconds |
| Dash | Subtle lead-ahead (camera moves 1 tile ahead of player) | Continuous |

## Quality Assurance Targets

| Metric | Target | Measurement Method |
|--------|--------|--------------------|
| Input-to-pixel latency | < 50 ms | Frame-by-frame capture with LED controller |
| Hitstop frame accuracy | +/- 0 frames | Deterministic tick validation in CI |
| Knockback distance accuracy | +/- 0.1 tiles | Unit test with FixedPoint64 math |
| i-frame consistency | 100% deterministic | Replay validation across platforms |
| Buffer reliability | No dropped inputs | Automated input sequence tests |
