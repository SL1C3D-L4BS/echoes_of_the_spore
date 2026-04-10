# Community Tree UI

## Overview

The Community Tree is a persistent, server-wide progression system visualized as a growing fungal tree in the Canopy hub. Players contribute Echoes (earned from delves) to unlock branches that provide global benefits. The UI must communicate progress, branch selection, and community participation.

## Tree Visualization

### Hub Representation

The Community Tree is a 3D-like structure rendered in 2D layers at the center of the Canopy hub:

| Element | Specification |
|---------|--------------|
| Tree trunk | Central vertical element, Mycelium Charcoal with Infection Cyan vein glow |
| Branches | 8-12 branches extending from trunk, unlocked progressively |
| Leaves/nodes | Branch endpoints glow when milestone reached |
| Growth animation | New growth extends over 5 seconds when contribution pushes past threshold |
| Scale | Occupies ~40% of hub vertical space. Grows taller as branches unlock. |

### Tree States

| State | Visual |
|-------|--------|
| Dormant branch | Dark silhouette, no glow, locked icon at branch tip |
| Active branch (contributing) | Branch glows faintly, progress bar visible along branch length |
| Milestone reached | Branch tip pulses with civilization color, particles emit |
| Fully grown branch | Solid glow, decoration at tip (fruit, crystal, flame, etc.) |
| Post-vote (FEED) | Tree overgrown, lush, bioluminescent excess |
| Post-vote (STARVE) | Tree withering, muted, falling particles |

## Branch Selection Interface

Accessed by interacting with the Community Tree in the Canopy hub.

```
+----------------------------------------------+
| COMMUNITY TREE                    Echoes: 340  |
|                                                |
|  [Tree visualization - interactive branches]   |
|                                                |
|  Selected: CULTIVATOR BRANCH                   |
|  Progress: 67% (14,230 / 21,000 Echoes)       |
|  Contributors: 892 players                     |
|                                                |
|  Current Milestone: Bloom Boost                |
|  Effect: +10% healing in Rot Garden            |
|  Next Milestone: Garden Restoration (21,000)   |
|  Effect: New rest room variant in Zone 5       |
|                                                |
|  [CONTRIBUTE 100]  [CONTRIBUTE ALL]  [BACK]   |
+----------------------------------------------+
```

### Branch List

| Branch | Civilization | Unlock Condition | Milestones |
|--------|-------------|-----------------|------------|
| Root Network | Rootborn | Default (always available) | 5 |
| Neural Expansion | Thinkers | 1,000 total server Echoes | 5 |
| Fermentation Vats | Brewers | 5,000 total server Echoes | 5 |
| Hive Reconstruction | Builders | 10,000 total server Echoes | 5 |
| Garden Restoration | Cultivators | 20,000 total server Echoes | 5 |
| Forge Rekindling | Smiths | 35,000 total server Echoes | 5 |
| Veil Research | Seekers | 50,000 total server Echoes | 5 |
| Progenitor Bond | All | Post-True Ending only | 3 |
| Growth (FEED) | -- | FEED outcome only | 5 |
| Remembrance (STARVE) | -- | STARVE outcome only | 5 |

## Contribution Interface

| Element | Specification |
|---------|--------------|
| Contribute button | Two options: fixed amount (100 Echoes) or all available |
| Contribution animation | Echoes float from player UI to tree branch (particle trail, 1.5s) |
| Audio | Crystalline deposit sound, pitch rises with contribution size |
| Confirmation | No confirmation modal for contributions under 500. Modal for 500+. |
| Cooldown | None. Players can contribute as often as they have Echoes. |

## Milestone Popups

When a branch milestone is reached, all online players see a celebration:

### Popup Layout

```
+----------------------------------------------+
|  +------------------------------------------+  |
|  | [Branch Icon]  MILESTONE REACHED          |  |
|  |                                            |  |
|  | Cultivator Branch: Bloom Boost             |  |
|  | +10% healing in Rot Garden for all players |  |
|  |                                            |  |
|  | Top Contributors:                          |  |
|  | 1. PlayerName (2,400 Echoes)               |  |
|  | 2. PlayerName (1,800 Echoes)               |  |
|  | 3. PlayerName (1,200 Echoes)               |  |
|  |                                            |  |
|  | [CELEBRATE]                  [DISMISS]      |  |
|  +------------------------------------------+  |
+----------------------------------------------+
```

| Parameter | Value |
|-----------|-------|
| Display duration | 10s auto-dismiss if no interaction |
| Animation | Slide in from top, 0.3s |
| Audio | Celebration fanfare (2s) |
| Celebrate button | Triggers emote on player character |
| Context sensitivity | Deferred during combat (see CINEMATIC_MOMENTS.md) |

## Progress Indicators

### Branch Progress Bar

| Element | Specification |
|---------|--------------|
| Shape | Bar running along the branch in tree visualization |
| Fill color | Civilization color of the branch |
| Background | Void Black at 50% opacity |
| Percentage text | Shown on hover/select, Space Mono 14px |
| Milestone markers | Small nodes along the bar at 20%, 40%, 60%, 80%, 100% |

### HUD Mini-Indicator

During gameplay, a small Community Tree progress indicator appears in the HUD:

| Element | Specification |
|---------|--------------|
| Position | Bottom-right, above minimap |
| Size | 160x8 bar |
| Content | Current active branch progress |
| Visibility | Fades in on Echo pickup, fades out after 5s |
| Update | Real-time server sync every 30 seconds |

## Server Sync

| Parameter | Value |
|-----------|-------|
| Contribution sync | Immediate (client sends, server validates, broadcasts) |
| Progress poll | Every 30 seconds for HUD indicator |
| Milestone broadcast | Server push via WebSocket to all connected clients |
| Offline contributions | Not supported. Must be connected. |
| Conflict resolution | Server is authoritative. Client optimistic display, corrected on sync. |

## Implementation Notes

- Tree visualization uses `CommunityTreeRenderer` with procedural sprite composition
- Branch data fetched from server API: `GET /api/community-tree/{server_id}`
- Contribution endpoint: `POST /api/community-tree/{server_id}/contribute`
- Milestone events received via `CommunityTreeEventBus` subscribed to WebSocket channel
- Branch progress cached in `CommunityTreeCache` singleton, refreshed on hub entry and every 30s
- Tree growth animation driven by `TreeGrowthAnimator` interpolating branch length/glow properties
- Milestone popup managed by `NotificationSystem` (see NOTIFICATION_SYSTEM.md)
- Top contributor data fetched on milestone event, displayed in popup
