# Retention Hook Architecture

## Hook Taxonomy

### Immediate Hooks (End of Each Run)
| Hook | Mechanism | Target Persona |
|------|-----------|---------------|
| **"One more boon"** | Died with 2/3 boons, want to see full build | Optimizer |
| **Community Tree progress** | Contribution just pushed tree to 95% of next node | Explorer, Social |
| **Codex entry tease** | "3/5 conditions met for Sporeling Mastery" | Completionist |
| **Infection cliffhanger** | At Tier 49 (one tier from major voice change) | Social |
| **Heat unlock** | Cleared at Heat 15, Heat 16 adds new Condition | Competitive |

### Session Hooks (Between Sessions)
| Hook | Mechanism | Cadence |
|------|-----------|---------|
| **Wish Board update** | Community quest progress notification | Daily |
| **Community Tree milestone** | Server unlocked new zone/NPC while offline | Push notification |
| **Rite of Echoes schedule** | Next Rite in 4 hours, team needs 4th | 72-hour cycle |
| **Battle Pass daily** | 3 daily challenges refresh | 24 hours |
| **Friend activity** | "Maya cleared Zone 6 at Heat 12" | Async |

### Long-Term Hooks (Weeks to Months)
| Hook | Mechanism | Duration |
|------|-----------|----------|
| **Community Tree depth** | 100 nodes, months to complete | 6+ months |
| **Echo Codex** | 300+ entries, some require Heat 30+ | 3-6 months |
| **Heat mastery** | 50 levels of increasing difficulty | 2-4 months |
| **True Ending vote** | Lore Depth 20 triggers server-defining choice | 6+ months |
| **Seasonal content** | New boons, classes, Crests every 3 months | Ongoing |

## "One More Run" Engineering

### The Sunk Cost Loop
```
Run N fails at Room 18/25
  → Player saw promising boon combo forming
  → "I was so close, one more try"
  → Run N+1 starts with same class/crest
  → Different boon offerings create new possibility
  → "This build might be even better"
  → Repeat
```

### The Discovery Loop
```
Run N: Player uses Sporeling + Lithic Petrify boon
  → Discovers Petrify + Glacine Shatter = AoE chain reaction
  → "What happens with Verdant Bloom instead?"
  → Run N+1: Seeks Bloom boon specifically
  → Discovers Bloom + Scorch = spreading fire DoT
  → "What if I add Corrode?"
  → Combinatorial explosion drives experimentation
```

### The Social Loop
```
Player A clips infection voice moment → shares to Discord
  → Player B: "That happened to us at Tier 75, it gets worse"
  → Player A: "I need to see Tier 90"
  → Queues another run specifically to push Infection higher
  → Creates own clip → shares → cycle continues
```

## Anti-Churn Mechanisms

| Churn Reason | Detection Signal | Intervention |
|-------------|-----------------|--------------|
| **"I've seen everything"** | All zones cleared, playtime plateau | Community Tree gates new content progressively |
| **"Too hard"** | Death rate >80% over 10 runs | Adaptive difficulty (reduce enemy count, increase boon quality) |
| **"Friends stopped playing"** | Solo-only sessions for 7+ days | Matchmaking highlight, Discord LFG notification |
| **"Meta is stale"** | Same class/crest/boons for 20+ runs | Bi-weekly balance patches, seasonal boon rotations |
| **"I hit a wall"** | Same Heat level for 10+ attempts | Unlock hint system: "Try Decayer Catalyst with Corrode boons" |
| **"Nothing to work towards"** | Codex 90%+, Heat 40+ | Prestige system, cosmetic chase, community challenges |

## Retention KPI Targets

| Metric | Day 1 | Day 7 | Day 30 | Day 90 |
|--------|-------|-------|--------|--------|
| Retention rate | 100% | 55% | 30% | 18% |
| Sessions per day | 2.5 | 1.8 | 1.2 | 1.0 |
| Avg session length | 45min | 55min | 60min | 50min |
| Runs per session | 1.5 | 2.0 | 2.5 | 2.0 |
| Community Tree contribution | Low | Medium | High | Stable |
