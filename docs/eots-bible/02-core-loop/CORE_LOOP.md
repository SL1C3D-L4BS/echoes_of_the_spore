# 02 — CORE GAME LOOP & PLAYER FANTASY

## The Loop Structure

Echoes has three nested loops operating at different time scales.

### Loop 1: The Delve (10-40 minutes)

This is the "run." The atomic unit of play.

```
CANOPY HUB
  → Select Class + Crest + Echo Functions
  → Matchmake (solo or 2-4P)
  → Enter Zone gate
    → Procedurally generated room sequence
    → Combat encounters (earn Echoes + gain Infection)
    → Echo Boon choices at reward rooms
    → Optional: Fungal Fusion with partner(s)
    → Optional: Spore Plan (cooperative Turn())
    → Boss encounter
    → Clear or die
  → Return to Canopy
    → Deposit Echoes to Community Tree
    → Spend Echoes on personal upgrades (if any)
    → Review Echo Codex progress
    → Purify infection (costs Echoes) or keep it (keep power)
  → REPEAT
```

**Key Tension in Loop 1:** Infection accumulates during delves, making you more powerful but degrading communication. Purifying costs Echoes that could go to the Community Tree. Every delve ends with a tragedy-of-the-commons decision.

### Loop 2: The Server Arc (Days to Weeks)

The Community Skill Tree progresses as all players on a server contribute Echoes. Milestones unlock new zones, enemies, abilities, NPCs, and hub structures. The server's Canopy visually grows.

```
WEEK 1: Sporehaven + Cortex unlocked. Hub is a clearing.
WEEK 2-3: Fermentation Pits + Chitinhall unlocked. Hub gains markets.
WEEK 4-6: Rot Garden + Myco-Forge. Hub gains fast-travel + housing.
WEEK 8-12: Necroveil unlocked. Hub gains lore archives.
WEEK 16+: Necrospore Core. Lore branch approaches True Ending vote.
MONTH 6+: True Ending vote. Server state permanently changes.
```

**Key Tension in Loop 2:** The tree has 5 branches. Players must collectively decide which branch to prioritize. Expansion (new zones) vs Evolution (new abilities) vs Infrastructure (hub growth) vs Ecology (enemies/NPCs) vs Lore (narrative). Spore Pass holders get a vote on priority.

### Loop 3: The Mastery Arc (Weeks to Months)

After first clear, the Pact of the Mycelium (Heat system) unlocks. The Echo Codex provides 300+ long-term goals. Seasonal content arrives via Battle Pass.

```
FIRST CLEAR: Heat system unlocks
HEAT 10: First prestige cosmetic
HEAT 20: Advanced cosmetic tier
HEAT 30: Elite cosmetic tier  
HEAT 50: Mycelium Crown (ultimate prestige)
CODEX 100%: Requires hundreds of hours across all systems
SEASONAL: New Battle Pass content every 3 months
```

## Session Flow (Minute-by-Minute)

A typical 30-minute session for a duo:

| Time | Activity | System |
|---|---|---|
| 0:00 | Launch, enter Canopy hub | Hub, UI |
| 0:30 | Check Wish Board, grab a quest | Quest system |
| 1:00 | Equip Crest, set Echo Functions | Loadout |
| 1:30 | Matchmake with friend, enter Sporehaven | Matchmaking, Zone |
| 2:00 | Room 1: combat encounter, earn first boon | Combat, Boon |
| 4:00 | Room 3: discover Fossil Mural | Environmental storytelling |
| 6:00 | Room 5: Fusion Node — merge with partner | Fusion |
| 7:00 | Room 6: Fused combat, Spore Plan first use | Spore Plan |
| 10:00 | Room 8: infection crosses Tier 2, voice distorts | Infection, VOIP |
| 12:00 | Room 10: mini-boss | Combat, AI |
| 15:00 | Room 13: Duo Echo triggers from combined boons | Boon synergy |
| 18:00 | Room 15: separate from Fusion for a puzzle room | Fusion lifecycle |
| 20:00 | Room 17: re-fuse for boss approach | Fusion |
| 22:00 | Boss room: Spore Plan coordinated burst | Boss, Spore Plan |
| 25:00 | Boss clear. Mycelium Narrator comments. | Narrator |
| 26:00 | Return to Canopy. Deposit 847 Echoes. | Community Tree |
| 27:00 | Community Tree node 12 unlocks server-wide! | Persistence |
| 28:00 | Decide: purify infection or keep buffs for next run | Infection tension |
| 29:00 | Check Codex: 2 new entries completed | Codex |
| 30:00 | Queue next delve or log off | Session end |

## Failure States & Recovery

| Failure | Consequence | Recovery |
|---|---|---|
| Player HP reaches 0 (first time) | Overload: HP refills, one random Function disabled | Reach a Rest room 3+ rooms away to restore |
| Second overload | Another Function disabled | Same |
| Third overload | True death: return to Canopy | Echoes earned during run are kept (partial) |
| All party members die | Session ends, return to Canopy | Echoes earned are kept at 50% rate |
| Disconnect during delve | Rejoin within 2 minutes: resume state | After 2 min: AI takes over, then graceful exit |
| Disconnect during Fusion | Fused entity separates, DC'd player becomes AI-controlled for 30s | Reconnect restores control |

## The "One More Run" Hook

The combination of:
- Echo Boon randomization (different build every run)
- Community Tree progress (my run matters to the server)
- Infection escalation (I was SO close to using that power)
- Codex checklist (I'm 2 entries away from completing a category)
- Wish Board goals (the community quest is at 94%)

...creates multiple simultaneous "one more run" motivators that reinforce each other rather than competing.
