# Player Agency Design

## Decision Taxonomy

Every meaningful player decision falls into one of these categories:

### 1. Strategic Decisions (Before Run)
| Decision | Options | Reversibility | Impact |
|----------|---------|---------------|--------|
| Class choice | 4 classes | Per-run | Entire playstyle |
| Crest choice | 3 per class | Per-run (or mid-run with Evolution Depth 5) | Attack patterns, slot distribution |
| Echo Function loadout | 8 per class, pick 3-5 | Per-run | Active abilities available |
| Heat configuration | 15 Conditions × 5 ranks | Per-run | Difficulty and reward |
| Community Tree branch | 5 branches | Per-contribution | Server direction |

### 2. Tactical Decisions (During Run)
| Decision | Frequency | Time Pressure | Weight |
|----------|-----------|--------------|--------|
| Echo Boon selection | 3 per run | None (paused) | Build direction |
| Fusion: merge or stay solo? | 1-3 per run | Low (node interaction) | Control tradeoff |
| Silk Gauge: Heal or Weaver? | Every 20 kills | Medium (mid-combat) | Survival vs offense |
| Room path choice | 2-4 per run | Low | Risk vs reward |
| Spore Plan timing | 1-2 per run | High (must be full gauge) | Burst damage window |

### 3. Mechanical Decisions (Per Second)
| Decision | Frequency | Time Window | Skill Expression |
|----------|-----------|------------|------------------|
| Attack or dodge? | Every encounter | ~50ms | Commitment read |
| Light or heavy attack? | Per attack | ~100ms | DPS vs knockback |
| Dash direction | Per dash | Instant | Positioning |
| Combo continuation | Every 500ms | 500ms | Aggression uptime |
| Aim direction (Gunner in Fusion) | Continuous | Continuous | Target priority |

## Agency Preservation Rules

1. **No invisible dice rolls** — if the player's attack lands visually, it hits mechanically
2. **No hidden scaling** — damage numbers always show true values, multipliers visible in pause menu
3. **Boon selection is informed** — full effect description visible before choosing, no surprise mechanics
4. **Death is always explainable** — death recap shows damage source, HP timeline, what could have been different
5. **Community Tree contribution is individual** — each player sees exactly how much THEY contributed
6. **Heat is opt-in** — nobody forces difficulty on you, you toggle conditions yourself
7. **Infection is manageable** — purification is always available (at a cost), player controls the tradeoff
