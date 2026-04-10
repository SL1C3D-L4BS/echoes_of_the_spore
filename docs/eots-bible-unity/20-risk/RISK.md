# 20 — RISK REGISTER & CONTINGENCY PLANNING

## Risk Register

| ID | Risk | Probability | Impact | Mitigation | Contingency | Owner |
|---|---|---|---|---|---|---|
| R01 | Netcode instability at scale (>1000 CCU) | Medium | Critical | Invest in dedicated server architecture from Month 1. Budget for PlayFab/Photon evaluation. | Fall back to peer-hosted with relay server | Tech Director |
| R02 | Gen-AI asset quality inconsistency | Medium | High | 100% human curation gate in Year 1. 3-stage quality pipeline (automated + peer + playtest). | Reduce Gen-AI ratio from 70% to 30%, increase hand-crafted. Extend art timeline 2 months. | Lead Artist |
| R03 | Community Tree pacing (too fast or too slow) | High | Medium | Dynamic cost scaling based on server population. Tuning knobs exposed to live ops. | Emergency patch to adjust cost formula coefficients. | Lead Designer |
| R04 | Console certification delays | Medium | Medium | Begin porting Month 11. Allocate 3-month buffer before launch. | Delay console launch, ship PC first. | Producer |
| R05 | Content creator adoption failure | Low | High | Pre-launch creator program (50 creators at Beta). Built-in Clip Mode + Infection Replay. | Paid creator partnerships for launch month. Increase marketing spend. | Marketing |
| R06 | Market saturation from StS2/Mewgenics/Silksong | Low | Medium | Echoes targets different niche (action + multiplayer + persistence). Overlap is audience, not product. | Differentiate marketing on Fusion + Spore-Talk (unique mechanics). | Marketing |
| R07 | FixedPoint64 math bugs causing desyncs | Medium | Critical | Exhaustive unit tests with golden reference table. Automated desync detection in every playtest. | Emergency desync log system. Server-side replay for debugging. | Engine Programmer |
| R08 | VOIP quality issues (latency, echo, noise) | Medium | Medium | Use Opus with PLC. 60ms jitter buffer. Push-to-talk default. | Disable Spore-Talk voice effects at low quality connections. Fall back to text chat. | Audio Director |
| R09 | Scope creep (30+ systems in bible) | High | High | Strict MoSCoW prioritization. Vertical slice locks scope. Features not in VS are deferred. | Cut: Ecosystem sim (simplified to static spawns), Rite of Echoes (post-launch), Wish Board (post-launch). | Producer |
| R10 | Key personnel departure | Medium | High | Document everything (this bible). Cross-train on critical systems. | Hire contractor for specific gap. Defer features dependent on departed person. | Producer |
| R11 | Server infrastructure costs exceed budget | Medium | Medium | Start with 2-3 regions. Use spot instances for non-critical services. | Reduce max server population. Merge low-pop servers. | Backend Dev |
| R12 | Fusion mechanic is not fun | Low | Critical | Prototype in Month 2. Playtest weekly. Kill or redesign by Month 6 if not working. | Pivot to standard co-op (no fusion). Retain all other systems. | Lead Designer |

## Scope Control: MoSCoW Prioritization

### MUST HAVE (Ship-blocking)
- 4 solo classes with 1 Crest each (3 Crests per class is stretch)
- 6 Fused Classes (not 12 — 6 is half the matrix, covers all class pairings once)
- 4 Zones (Sporehaven, Cortex, Fermentation Pits, Necrospore Core)
- Echo Boon system (4 civilizations minimum, not 8)
- Community Skill Tree (3 branches minimum)
- Rollback netcode for 2P co-op
- Infection system (all 6 tiers)
- Basic Spore-Talk (proximity VOIP without DSP effects)
- Procedural room generation with validation
- 1 boss per zone

### SHOULD HAVE (Launch target)
- All 8 Zones
- All 12 Fused Classes
- All 8 civilizations + 28 Duo Echoes
- Full Community Tree (5 branches × 20 depths)
- Infection DSP chain (all tiers)
- Mycelium Narrator (minimum 400 lines)
- 4P co-op with rollback
- Heat system (Pact of the Mycelium)
- Spore Plan (cooperative Turn())
- 3 Crests per class

### COULD HAVE (Post-launch or stretch)
- Rite of Echoes (PvP)
- Ecosystem simulation (food chain)
- Mycelium Hunters (EMMI mechanic)
- Echo Codex (300+ entries)
- Wish Board
- Brand crossover cosmetics
- AI Director (Zone 8)
- Full 1,200-line narrator script

### WON'T HAVE (Out of scope)
- Native mobile build (cloud-streaming only)
- PvP ranked ladder
- Player-placed building (hub evolves automatically)
- User-generated content / modding (future consideration)
- VR support
