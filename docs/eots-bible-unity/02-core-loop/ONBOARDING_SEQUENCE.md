# Onboarding Sequence

## Design Philosophy
**"Show, don't tell. Teach through play, not popups."**

No tutorial screens. No floating text boxes. Every mechanic is introduced through level design, enemy placement, and environmental cues. The first 3 runs are a carefully sequenced teaching arc.

## Run 1: "The First Descent" (Solo, Zone 1 only, ~8 minutes)

| Room | Teaches | Method |
|------|---------|--------|
| 1 | Movement + Jump | Single platform gap, can't progress without jumping |
| 2 | Dash | Wall blocking path, dash through narrow gap |
| 3 | Light Attack | Single Puffcap (1 HP), explodes if you don't hit it first |
| 4 | Heavy Attack | Armored Puffcap (light attack bounces, heavy breaks armor) |
| 5 | Combo | 3 Spore Mites in a line, combo kills all 3 efficiently |
| 6 | Silk Gauge + Heal | Take guaranteed 1 damage from scripted hazard, Silk is full, Bind prompt glows |
| 7 | Echo Boon | First Reward Room, only 1 boon offered (always Lithic Strike for guaranteed impact) |
| 8 | Dodge timing | Tier 2 Chitin Knight intro, telegraphed attack pattern (parryable) |
| 9 | Environmental hazard | Acid pool with visual warning, safe path visible |
| 10 | Boss: Spore Mother (simplified) | 2-phase only, clear attack patterns, generous i-frames |

### Run 1 Narrative
- Mycelium Voice speaks for first time in Room 1: *"Another one. They always come."*
- First Fossil Mural in Room 5 (can't miss it, blocks the path)
- On death: *"Death is not the end here. Nothing ends."* (reassuring tone)
- On boss clear: *"You survived. How... uncommon."*

## Run 2: "The Partner" (Co-op tutorial, Zone 1, ~10 minutes)

**Triggered when:** Player queues co-op for the first time

| Room | Teaches | Method |
|------|---------|--------|
| 3 | Proximity awareness | Partner's health bar visible, proximity voice icon |
| 5 | Spore-Talk | Prompt to speak, partner hears with slight echo (Infection Tier 0) |
| 8 | Fusion Node | Glowing node, both players must interact, Fusion tutorial begins |
| 9 | Fused Movement (Pilot) | Pilot moves, Gunner aims, simple combat room |
| 10 | Fused Combat | Boss with Fusion active, teaches role coordination |

### Key Moments
- First voice corruption at Infection Tier 11: both players hear the echo effect
- If players don't fuse voluntarily by Room 8, Mycelium Voice says: *"Why do you resist the joining?"*

## Run 3: "The Choice" (Full loop, any zone)

| Element | Teaches |
|---------|---------|
| Community Tree | Post-run Echo deposit, see server progress bar move |
| Infection purification | Cost/benefit decision explained through UI |
| Heat system | Unlocks after first full clear, tooltip on Pact shrine |
| Codex | First entry unlocked, notification with "300+ to discover" |

## Onboarding Metrics

| Metric | Target | Red Flag |
|--------|--------|----------|
| Run 1 completion rate | > 85% | < 70% (too hard) |
| Run 1 time to first kill | < 30 seconds | > 60 seconds (controls confusing) |
| Run 2 Fusion activation rate | > 90% | < 75% (node not clear enough) |
| Run 3 Community Tree interaction | > 80% | < 60% (UI not guiding) |
| Day 1 to Day 2 retention | > 65% | < 50% (onboarding failed) |
