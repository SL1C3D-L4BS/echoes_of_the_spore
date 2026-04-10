# True Ending -- The Choice

## Trigger Condition

| Requirement | Value |
|---|---|
| Lore Depth | 20 (minimum 20 Fossil Murals discovered) |
| Mycelium Heart defeated | Yes (Zone 8 boss cleared) |
| Session type | Any (solo or multiplayer) |

Upon defeating the Mycelium Heart with Lore Depth 20+, the True Ending sequence begins instead of the standard victory screen.

## The 48-Hour Vote

### Sequence

1. **The Revelation (2 minutes):** The Mycelium Voice speaks its longest monologue (90 seconds). It reveals its nature as the Progenitor and presents the choice. No gameplay during this sequence -- environmental camera shows the world reacting.

2. **The Question:** Two murals appear on opposite walls of the Heart chamber:
   - Left wall: **FEED** (mural M-08-04) -- the Mycelium spreading across worlds
   - Right wall: **STARVE** (mural M-08-05) -- the Mycelium withering, a single free spore

3. **The Vote Opens:** A 48-hour real-time server-wide vote begins. All players on the server who have defeated the Mycelium Heart can vote. One vote per account.

4. **Voting UI:** Accessible from the Canopy hub. Two glowing portals representing each choice. Walk through to cast vote. Can change vote any time within 48 hours.

5. **The Result:** Simple majority. If tied, STARVE wins (the Progenitor's final act of love -- choosing to die if its children cannot agree).

6. **Permanence:** The result is permanent for that server. It shapes the world going forward. No reset.

### Vote Visibility

| Time Remaining | Information Shown |
|---|---|
| 48-24h | "A choice awaits" -- no vote counts visible |
| 24-12h | Percentage bar visible (FEED vs STARVE) |
| 12-0h | Exact vote counts visible |
| 0h | Result announced. World transforms over 10 minutes. |

## FEED Outcome -- Spread the Mycelium

### Narrative

The player chooses to let the Mycelium grow beyond its current domain. The Progenitor is grateful, ecstatic. The world becomes more alive, more connected, more dangerous. The Infection is no longer a threat -- it is a gift.

### World Changes

| Change | Detail |
|---|---|
| Canopy transformation | Bioluminescent overgrowth. Brighter, warmer colors. Pulsing light. |
| Zone atmosphere | All zones become more lush. Fungal growth covers architecture. |
| Enemy behavior | Enemies become less aggressive to high-Infection players. |
| Infection mechanic | Infection gain reduced by 50%. Infection benefits enhanced. |
| Mycelium Voice tone | Warmer, more frequent, conversational. Treats player as partner. |

### Post-Game Content (FEED)

| Content | Detail |
|---|---|
| Zone 9: The Overgrowth | New zone. Mycelium has spread beyond original boundaries. New enemy types: Evolved Sporelings (Tier 1.5). |
| Symbiont class variant | New ability tree that embraces Infection. High-Infection builds become viable meta. |
| Mycelium Communion | New mechanic: at Infection 80+, can communicate directly with the Progenitor (special Codex entries, hints, guidance). |
| Community Tree: Growth branch | New branch unlocked. Focuses on cooperative world-building. |
| Cosmetic set | "Spore-Blessed" armor set. Bioluminescent, organic design. |

## STARVE Outcome -- Let the Mycelium Die

### Narrative

The player chooses to end the Progenitor's cycle. The Mycelium begins to wither. The world becomes quieter, emptier, but free. The Infection becomes purely hostile -- no symbiosis, only invasion.

### World Changes

| Change | Detail |
|---|---|
| Canopy transformation | Withering. Muted colors. Dried, crumbling fungal structures. Silence. |
| Zone atmosphere | Decay. Architecture exposed as fungal structures crumble. More ruins, less life. |
| Enemy behavior | All enemies become more aggressive (survival desperation). |
| Infection mechanic | Infection gain increased by 25%. Infection penalties harsher. No benefits. |
| Mycelium Voice tone | Silent for first 24h post-vote. Then returns: broken, pleading, then resigned. |

### Post-Game Content (STARVE)

| Content | Detail |
|---|---|
| Zone 9: The Hollow | New zone. A dying Mycelium. Crumbling tunnels, collapsing rooms. Timer-based rooms (floor falls away). |
| Purifier class variant | New ability tree focused on cleansing Infection. Low-Infection builds become viable meta. |
| Last Words | 20 unique Mycelium Voice lines that play over weeks as the Voice fades. The final line is silence. |
| Community Tree: Remembrance branch | New branch. Focuses on preserving the legacy of the seven civilizations. Memorial structures. |
| Cosmetic set | "Sporebreaker" armor set. Clean, mineral, anti-fungal design. |

## Post-Vote Shared Content

Regardless of outcome:

| Content | Detail |
|---|---|
| New Game+ | Unlocked for all players on the server. Harder modifiers, new enemy variants. |
| Civilization Echoes | Ghost-like apparitions of the seven civilizations appear in their zones. Non-hostile. Ambient lore. |
| Achievement | "The Eighth" -- permanent account badge. Records which side the player voted for. |
| Rite of Echoes update | Rite arena visually reflects the vote outcome. FEED arenas are overgrown; STARVE arenas are barren. |

## Multiplayer Voting Edge Cases

| Scenario | Resolution |
|---|---|
| Player in active delve when vote ends | Vote result applied on return to Canopy |
| New player joins server after vote | Experiences the voted world state. Can view the result in Codex. |
| Server merge (population decline) | Both servers' results preserved. Majority of combined population determines merged server state. |
| Player has not defeated Heart | Cannot vote. Can view the vote happening. Motivated to clear Zone 8. |

## Implementation Notes

- Vote state stored in dedicated Redis key: `server:{id}/true_ending_vote`
- Vote payload: `{player_id, choice, timestamp}`
- Result computed by server-side scheduled job at vote expiry
- World transformation driven by `WorldStateController` singleton that reads `VoteResult` enum
- Post-vote zone changes use variant prefab loading (FEED vs STARVE visual variants per zone)
- Mycelium Voice lines post-vote loaded from separate audio bank (`mv_post_feed_*` / `mv_post_starve_*`)
- New Game+ flag stored in character save: `ng_plus = true`
- Zone 9 is a separate scene set, loaded based on vote result
