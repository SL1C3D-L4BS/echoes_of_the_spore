# Session Flow Specification

## The Delve — Minute-by-Minute Breakdown

```
TIME    PHASE               PLAYER STATE          SYSTEM ACTIVE
─────────────────────────────────────────────────────────────
00:00   Hub (Canopy)         Preparing loadout      Menu UI, Social
00:30   Class Select         Choosing class/crest   Loadout System
01:00   Matchmaking          Waiting for party      Redis matchmaking
01:30   Zone Entry           Cinematic transition   Scene loading, proc-gen
02:00   Room 1 (Combat)      First encounter        ECS combat, Infection +1
03:30   Room 2 (Combat)      Building combo          Silk gauge filling
05:00   Room 3 (Reward)      Echo Boon selection    Boon UI, decision point
05:30   Room 4 (Combat)      Testing new boon       Boon effects active
07:00   Room 5 (Puzzle)      Environmental puzzle   Physics, triggers
08:00   Room 6 (Transition)  Moving deeper          Zone atmosphere shift
08:30   Room 7 (Combat)      Tier 2 enemy intro     Behavior tree AI
10:00   Room 8 (Rest)        Healing, planning      Safe zone, Silk refill
10:30   Room 9 (Combat)      Infection Tier 2       Voice DSP activates
12:00   Room 10 (Reward)     Second boon choice     Boon synergy evaluation
12:30   Room 11-15 (Mixed)   Deepening challenge    All systems active
18:00   Fusion Node          Merge with partner     Fusion state machine
19:00   Room 16-20 (Fused)   Fused combat           Dual-brain input
24:00   Room 21 (Reward)     Third boon choice      Build crystallizing
25:00   Room 22-25 (Mixed)   Final approach          AI Director (Zone 8)
30:00   Boss Room            Boss encounter          3-phase boss fight
35:00   Boss Clear           Victory or death        Reward calculation
35:30   Return to Canopy     Echo deposit            Community Tree update
36:00   Post-Run             Purification decision   Infection management
37:00   Session End          Codex check, next run    Retention hooks fire
```

## Emotional Arc per Session

```
Excitement ▲
           │         ╱╲      ╱╲ BOSS
           │   ╱╲   ╱  ╲    ╱  ╲
           │  ╱  ╲ ╱ F  ╲  ╱    ╲
           │ ╱    ╲╱ U   ╲╱      ╲
           │╱      S  S           ╲
           │       I  I            ╲ POST-RUN
           │       O  O             ╲ DECISION
           │       N  N              ▼
           └──────────────────────────── Time
           Hub  Combat  Boon  Fusion  Boss  Deposit
```

## Decision Points per Session (Target: 12-15)

| # | Decision | Tension Source |
|---|----------|---------------|
| 1 | Class + Crest selection | Loadout commitment |
| 2 | Echo Boon #1 (3 options) | Build direction |
| 3 | Room path choice (branching) | Risk vs reward |
| 4 | Echo Boon #2 | Synergy evaluation |
| 5 | Fusion: merge or stay solo? | Control tradeoff |
| 6 | Fusion role assignment | Team coordination |
| 7 | Spore Plan activation timing | Resource management |
| 8 | Echo Boon #3 | Build completion |
| 9 | Boss strategy (attack patterns) | Skill expression |
| 10 | Silk Gauge: heal or Weaver Technique? | Offensive vs defensive |
| 11 | Infection management: purify or keep? | Echo cost vs power |
| 12 | Community Tree branch contribution | Server impact |
| 13 | Next run: same build or experiment? | Exploration vs exploitation |
