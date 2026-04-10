# Canopy Hub -- Persistent Social Space

## Overview

| Property | Value |
|---|---|
| Function | Social hub, loadout prep, quest board, Community Tree, lore archive, matchmaking, purification, crafting |
| Max Visible Players | 200 (overflow: instanced duplicate hubs) |
| Sync Method | Supabase Realtime (state), Redis 2 Hz (player positions) |
| Music | G major, 70 BPM, acoustic fingerpicking + ambient nature + wind chimes |
| Color Temperature | Warm daylight amber (#FFD59E) + living green (#4CAF50) |

## Evolution Stages

The Canopy transforms as the server's Community Tree grows. Each stage unlocks new structures, NPCs, and visual complexity.

| Stage | Tree Depth | Visual State | Structures Unlocked | Ambient Change |
|---|---|---|---|---|
| Seed | 0 | Bioluminescent clearing, single stone bench, sapling | Bench, Zone 1 gate | Firefly particles, distant wind |
| Sprout | 1-5 | Small fungal shelters sprout, paths defined by root growth | Basic Merchant, Wish Board | Bird-like calls, water trickle |
| Growth | 6-10 | Canopy roof begins forming, lantern fungi light paths | Blacksmith, Lore Pedestal, Zone 2-4 gates | Light rain effect, deeper ambience |
| Bloom | 11-15 | Full canopy overhead, branching paths, flower beds | Fast-travel nodes, Purification Pool, Housing plots | Pollen particles, layered music |
| Apex | 16-19 | Metropolis-scale fungal city, multi-level architecture | All services, Rite Arena, Cross-server portal | Full orchestra ambient, dynamic weather |
| Ascended | 20 (Light vote) | Flourishing growth, golden light, flowers everywhere | Final cosmetics vendor, Legacy Wall | Choir layer, warm lens flare |
| Withered | 20 (Dark vote) | Beautiful decay, bioluminescent fungi on ruins | Final cosmetics vendor (dark palette), Entropy Shrine | Reversed choir, cold particle fog |

## Hub Layout Map

```
                    [Cross-Server Portal] (Apex+)
                           |
                    [Rite Arena] (Apex+)
                           |
         [Housing]----[Upper Canopy]----[Lore Archive]
            |              |                 |
      [Blacksmith]---[Community Tree]---[Purification Pool]
            |              |                 |
     [Basic Merchant]-[Central Plaza]-[Wish Board]
            |              |                 |
      [Zone Gates]---[Spawn Point]----[Matchmaking Board]
                           |
                    [Tutorial Path]
```

### Area Descriptions

| Area | Size (tiles) | Capacity | Function |
|---|---|---|---|
| Spawn Point | 20x20 | 30 players | Entry point. Ambient tutorial prompts for new players. |
| Central Plaza | 40x40 | 80 players | Main gathering space. Community Tree at center. |
| Zone Gates | 30x15 | 20 players | Gate podiums arranged in a semicircle. Locked gates glow dim; unlocked gates pulse. |
| Upper Canopy | 30x30 | 40 players | Elevated platform accessible via fungal elevator (Growth+). Premium services. |
| Tutorial Path | 15x60 | 10 players | Linear path from spawn to plaza. First-time players only; collapses after first zone clear. |
| Housing Plots | 40x40 | 20 players (instanced per plot) | Player-owned fungal pods. Cosmetic customization only. Bloom+ stage. |

## Community Tree Visualization

The Community Tree is the hub's centerpiece -- a massive fungal structure that grows as the server community progresses.

| Tree Component | Growth Trigger | Visual Effect |
|---|---|---|
| Trunk Diameter | Total server zone clears | Thickens from sapling (1 tile) to ancient tree (8 tiles) |
| Branch Count | Unique players contributed | New branches sprout; each branch bears a contributor's glyph |
| Leaf Density | Community Echoes donated | Canopy fills in; dense = thriving server |
| Bioluminescence Color | Dominant community Infection level | Low infection: warm gold. High infection: cold purple. |
| Root Network | Zones unlocked server-wide | Roots extend toward corresponding zone gates |
| Flower Blooms | Community milestones (first boss kill, etc.) | Permanent flowers appear commemorating achievements |

## NPC Roster

### Permanent NPCs

| NPC | Name | Location | Stage Available | Function |
|---|---|---|---|---|
| Basic Merchant | Tendril | Central Plaza | Sprout | Sells consumables (health spores, distraction items, antidotes). Prices scale with Community Tree depth. |
| Blacksmith | Korrath | Upper Canopy | Growth | Sells equipment. Inventory refreshes every 4 hours. Offers repair services (restore durability). |
| Lore Keeper | Whisper | Lore Archive | Growth | Replays discovered Fossil Murals. Sells lore fragments for Echoes. Provides zone history on request. |
| Purification Priestess | Lumina | Purification Pool | Bloom | Reduces Infection by 10 per visit (1 visit per delve). Offers Purification Rites (group cleansing events). |
| Wish Board Keeper | Sprig | Wish Board | Sprout | Manages community requests. Players post wishes; others fulfill them for bonus Echoes. |
| Matchmaking Herald | Clarion | Matchmaking Board | Seed | Facilitates party formation. Displays active LFG requests. Quick-match for zone runs. |
| Cosmetics Vendor | Veil | Upper Canopy | Apex | Sells visual customization (skins, auras, trail effects). Earnable currency only, no premium. |

### Rotating NPCs

| NPC | Name | Rotation | Function |
|---|---|---|---|
| Wandering Scholar | Varies (7 named) | Daily rotation | Offers a daily challenge quest. Rewards: rare boons, cosmetics, lore. |
| Spore Trader | Myx | Appears every 3rd day for 8 hours | Trades rare materials. Inventory is random. Accepts Echoes and zone-specific drops. |
| Memory Weaver | Glacine Ghost | Weekends only | Tells stories of the lost civilizations. Listening grants a minor XP buff (5%, 1 hour). Non-interactive lore delivery. |

## Purification Shrine Specification

| Property | Value |
|---|---|
| Location | Western edge of Central Plaza, surrounded by clean water |
| Activation | Interact + 3s channel (interruptible by taking damage) |
| Effect | Reduces Infection by 10 points (minimum 0) |
| Cooldown | Once per delve (resets when entering a zone) |
| Visual | Water glows white during channel. Player's Infection visual effects dim. |
| Group Rite | Priestess Lumina conducts a group cleansing (4+ players) every 2 hours. Reduces Infection by 20 for all participants. |
| Lore Note | The pool is Glacine-made -- one of their last gifts before the Rot Garden consumed them. |

## Wish Board Specification

| Property | Value |
|---|---|
| Location | Eastern edge of Central Plaza |
| Post Cost | 10 Echoes |
| Max Active Wishes | 3 per player |
| Wish Types | Help Request (zone carry), Item Request (specific drop), Lore Hunt (find specific mural), Social (looking for guild) |
| Fulfillment Reward | Fulfiller receives 2x the poster's Echo investment + a Gratitude Token (cosmetic currency) |
| Display | Physical board with pinned notes. Each note shows player name, wish type, and timestamp. Oldest wishes glow brighter. |
| Expiry | Wishes expire after 72 hours if unfulfilled. Echoes refunded. |

## Hub Services Table

| Service | Location | Stage | Cost | Effect |
|---|---|---|---|---|
| Rest Bench | Spawn Point | Seed | Free | Passive HP regen (5%/s) while seated |
| Zone Gate | Zone Gates area | Varies | Free | Teleports to zone entrance; requires ability unlock |
| Fast Travel | Upper Canopy nodes | Bloom | 5 Echoes | Instant teleport between hub areas |
| Equipment Repair | Blacksmith | Growth | 15-50 Echoes | Restores equipment durability to 100% |
| Boon Reroll | Lore Keeper | Growth | 30 Echoes | Rerolls one equipped boon (random replacement) |
| Infection Reduction | Purification Pool | Bloom | Free | -10 Infection per delve |
| Housing Access | Housing Plots | Bloom | 200 Echoes (one-time) | Personal cosmetic space |
| Cross-Server Visit | Portal | Apex | 50 Echoes | Visit another server's hub for 30 min |
| Rite Arena | Rite Arena | Apex | Free | PvE challenge arena with leaderboards |

## Art Direction

**Palette:** Warm daylight amber (#FFD59E) for all lighting. Living green (#4CAF50) for vegetation. The hub should feel like perpetual golden hour. Structures use warm wood and fungal-ivory tones. Water is crystal clear with a slight blue tint (#B3E5FC).

**Evolution Visual Language:** Each stage adds complexity without changing the core layout. Seed stage is a single clearing. By Apex, the same clearing is the ground floor of a multi-level fungal city. The transformation should feel organic -- growth, not construction.

**Player Density:** At 200 players, the hub must remain legible. Player nameplates fade beyond 15 tiles. Distant players render as silhouettes. Particle effects from cosmetics are culled beyond 10 tiles.

**Day/Night Cycle:** 30-minute real-time cycle. Night increases bioluminescence, changes ambient audio to nocturnal sounds, and shifts color temperature to cool blue (#B0C4DE). Certain NPCs (Memory Weaver) only appear at night.

**Audio:** Acoustic fingerpicking base layer. Each stage adds instruments: Sprout adds flute, Growth adds cello, Bloom adds full strings, Apex adds choir. The Community Tree emits a low harmonic hum that changes pitch with its growth. Player footsteps change sound based on surface (root, stone, water, moss).

## Hub Design Guidelines

1. **Navigation Clarity:** A new player at Spawn Point should be able to see the Community Tree and at least one Zone Gate within 3 seconds of loading in. Signposting is mandatory.
2. **Social Friction Reduction:** The Central Plaza must be large enough for 80 players without feeling crowded. NPC interaction zones have a 3-tile exclusion radius where other players render as transparent.
3. **Evolution Continuity:** When the hub upgrades to a new stage, the transition should happen gradually over 60s with a growth animation visible to all online players. This is a community moment.
4. **AFK Management:** Players idle for 15+ minutes are moved to a low-priority rendering layer (silhouette only). After 30 minutes, disconnected to overflow hub.
5. **Accessibility:** All hub areas must be reachable without platforming. The Upper Canopy elevator is a single button press, not a skill challenge.
6. **Performance Budget:** Hub scene must maintain 60 FPS with 200 visible players on minimum spec hardware. LOD aggressively for distant players and decorative elements.
7. **No Pay-to-Win:** All services use earnable in-game currency. The Cosmetics Vendor uses Gratitude Tokens (earned by helping others via Wish Board). No premium currency exists.
