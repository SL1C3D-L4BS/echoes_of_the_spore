# Staffing Plan

## Team Ramp

| Phase | Months | Headcount | Monthly Burn | Key Hires |
|-------|--------|-----------|-------------|-----------|
| Pre-Prod | 1-4 | 8 | $120K | Core team (TD, Lead Design, Lead Art, 2 Prog, Rust, Audio, Producer) |
| Vertical Slice | 5-10 | 15 | $225K | +3 Programmers, +2 Artists, +1 Designer, +1 QA |
| Alpha | 11-16 | 20 | $300K | +2 Artists, +1 Backend, +1 Level Designer, +1 QA |
| Beta | 17-20 | 25 | $375K | +2 QA, +1 Community, +1 Localization, +1 Marketing |
| Polish/Launch | 21-24 | 25+ | $375K | External: VO studio, localization vendor, cert support |
| Live Ops | 25+ | 15 | $225K | Reduce to maintenance team |

## Core Team Roles (Pre-Prod, Month 1)

| Role | Responsibility | Skills Required |
|------|---------------|-----------------|
| **Technical Director** (@the_architect) | Architecture, ECS, netcode, proc-gen pipeline, build system | C#, Rust, networking, Unity internals |
| **Lead Game Designer** | Combat feel, class balance, progression, Community Tree | GDD authoring, Hades/Dead Cells analysis, spreadsheet mastery |
| **Lead Artist** | Art direction, sprite art, animation, shader prototypes | Aseprite, Shader Graph, pixel art, animation principles |
| **Unity Programmer** | Bridge layer, scene management, UI systems, platform | Unity 6, C#, URP, Input System |
| **Gameplay Programmer** | Combat systems, AI, class implementation, boons | C#, ECS patterns, state machines, behavior trees |
| **Rust Developer** | Proc-gen engine, FFI bridge, WASM target | Rust, no_std, C FFI, algorithm design |
| **Audio Designer** | FMOD integration, adaptive music, SFX, Spore-Talk DSP | FMOD Studio, Opus codec, DSP, music composition |
| **Producer** | Schedule, budget, milestones, vendor management, hiring | Project management, Agile, game production |

## Budget Allocation ($10M Total)

| Category | Amount | % |
|----------|--------|---|
| Engineering (salaries) | $3,500,000 | 35% |
| Art & Animation | $2,500,000 | 25% |
| Design & Production | $1,500,000 | 15% |
| Audio (VO, music, SFX) | $800,000 | 8% |
| QA | $700,000 | 7% |
| Marketing | $700,000 | 7% |
| Infrastructure (servers, tools, licenses) | $300,000 | 3% |

## Unity Licensing
Pro: $2,200/seat/year × 25 seats × 2 years = **$110,000** (1.1% of budget)
No royalties (unlike Unreal's 5% after $1M).

## Hiring Timeline

| Month | Hire | Rationale |
|-------|------|-----------|
| 1 | Core 8 | Foundation team |
| 5 | +3 Programmers | Vertical slice scope requires parallel workstreams |
| 5 | +2 Artists | Zone art production begins |
| 5 | +1 Designer | Level design for Zone 1-2 |
| 5 | +1 QA | Automated test maintenance |
| 11 | +2 Artists | Zone 3-8 art production |
| 11 | +1 Backend | Supabase + Redis infrastructure |
| 11 | +1 Level Designer | Zone 3-8 layout |
| 11 | +1 QA | Console testing |
| 17 | +2 QA | Beta testing surge |
| 17 | +1 Community Manager | Discord, Reddit, social |
| 17 | +1 Localization Lead | 10 languages coordination |
| 17 | +1 Marketing | Launch campaign execution |
