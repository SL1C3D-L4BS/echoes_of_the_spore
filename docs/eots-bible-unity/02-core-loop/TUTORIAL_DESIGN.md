# Tutorial Design Philosophy

## Rule: Zero Popups, Zero Text Walls

Every mechanic taught through environmental design, enemy placement, and natural discovery. The world IS the tutorial.

## Teaching Principles

| Principle | Implementation |
|-----------|---------------|
| **Introduce one mechanic at a time** | Each room in Run 1 teaches exactly one action |
| **Safe failure first** | Player encounters mechanic in low-stakes room before combat use |
| **Show don't tell** | Level geometry forces the action (gap requires jump, wall requires dash) |
| **Reward immediately** | Using a mechanic correctly gives instant positive feedback (SFX, VFX, kill) |
| **Repeat in escalation** | Mechanic reappears in harder contexts over next 3-5 rooms |

## Mechanic Introduction Order

| Run | Room | Mechanic | Teaching Method |
|-----|------|----------|----------------|
| Run 1 | 1 | Move left/right | Flat corridor to exit |
| Run 1 | 2 | Jump | Platform gap (can't cross without jumping) |
| Run 1 | 3 | Dash | Narrow gap in wall (can't fit without dashing) |
| Run 1 | 4 | Light attack | Single Puffcap blocking path (1HP, explodes if ignored) |
| Run 1 | 5 | Heavy attack | Armored Puffcap (light bounces off, heavy breaks through) |
| Run 1 | 6 | Combo | 3 Spore Mites lined up (combo kills all efficiently) |
| Run 1 | 7 | Silk Gauge + Heal | Scripted 1-damage hazard, full Silk gauge, Bind prompt |
| Run 1 | 8 | Echo Boon | First Reward Room, 1 boon offered (guaranteed strong) |
| Run 1 | 9 | Dodge timing | Chitin Knight intro (telegraphed, parryable) |
| Run 1 | 10 | Boss | Spore Mother (simplified 2-phase) |
| Run 2 | 1 | Co-op awareness | Partner's HP bar visible |
| Run 2 | 3 | Spore-Talk | Voice prompt, partner hears echo |
| Run 2 | 8 | Fusion | Glowing Fusion Node, both interact |
| Run 2 | 9 | Fused combat | Simple room while fused |
| Run 3 | Post | Community Tree | Echo deposit, server progress |
| Run 3 | Post | Infection purify | Cost/benefit UI |
| Run 3 | Post | Heat system | Pact shrine unlocks |

## What We Never Do

1. **No tooltip overlays** that pause gameplay
2. **No "Press X to..." prompts** on screen (except first-ever input)
3. **No mandatory tutorial zone** — Run 1 IS the tutorial, but it's a real delve
4. **No tutorial skip** — because it's not labeled as a tutorial
5. **No repeated tutorials** — once cleared, Run 1 is a normal zone run
6. **No handholding after Run 3** — player should know all core mechanics

## Contextual Hints (Settings-Toggleable)

For players who want guidance, an optional hint system:
- Small icon appears near interactable objects (subtle, not intrusive)
- Narrator occasionally says "I wonder what would happen if you..."
- Disabled by default for returning players
- Always available in Settings > Gameplay > Hints
