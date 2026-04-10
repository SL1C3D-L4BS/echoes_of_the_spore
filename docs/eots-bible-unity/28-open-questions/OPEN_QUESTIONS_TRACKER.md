# Open Questions Tracker

## Unresolved Design Questions

| # | Question | Owner | Priority | Deadline | Status |
|---|----------|-------|----------|----------|--------|
| 1 | Should Fusion be forced at certain difficulty levels, or always optional? | Design | High | VS Milestone | Open |
| 2 | What happens to Infection when fusing? Average, max, or sum capped? | Design | High | Month 4 | Open |
| 3 | Should Rite of Echoes have seasons/ranked reset? | Design | Medium | Alpha | Open |
| 4 | Can players trade cosmetics via Auction House (Infra Depth 13)? | Design/Econ | Medium | Alpha | Open |
| 5 | Switch performance: 30fps render acceptable? Or target 60fps with reduced VFX? | Tech | High | Month 6 | Open |
| 6 | Should Mycelium Hunters be killable at extreme Heat (50)? | Design | Low | Beta | Open |
| 7 | True Ending: can servers re-vote, or is it permanent? | Narrative | High | Alpha | Open |
| 8 | Cross-server portal (Infra Depth 20): separate matchmaking or shared? | Backend | Medium | Beta | Open |
| 9 | Modding support: should we expose proc-gen parameters to players? | Tech | Low | Post-Launch | Open |
| 10 | Voice actor casting: single narrator or multiple voices per civilization? | Audio | Medium | Month 8 | Open |

## Resolved Questions (Move here when decided)

| # | Question | Decision | Date | ADR |
|---|----------|----------|------|-----|
| R1 | DOTS or custom ECS? | Custom ECS (determinism) | 2026-04-10 | ADR-001 |
| R2 | Unity or Godot? | Unity 6.3 LTS | 2026-04-10 | ADR-005 |
| R3 | Unreal or not? | No (royalties, 3D-focused) | 2026-04-10 | ADR-005 |
| R4 | float or fixed-point? | FixedPoint64 (32.32 int64) | 2026-04-10 | ADR-002 |
