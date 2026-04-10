# Menu Flow

## Overview

The menu system follows a linear flow from boot to gameplay with hub-accessible side screens. Every screen is reachable within 3 inputs from the Canopy hub. Back navigation is always available via Escape / B button.

## Master Flow

```
Boot -> Splash -> Title Screen -> Main Menu
  Main Menu -> Play
    Play -> Class Select -> Crest Select -> Loadout -> Matchmaking -> Loading -> Gameplay
  Main Menu -> Community Tree
  Main Menu -> Echo Codex
  Main Menu -> Wish Board
  Main Menu -> Purification Pool
  Main Menu -> Settings
  Main Menu -> Quit

Gameplay (In-Delve) -> Pause Menu
  Pause Menu -> Resume
  Pause Menu -> Settings
  Pause Menu -> Abandon Delve -> Confirm -> Canopy Hub

Gameplay -> Death -> Respawn / Spectate / Abandon
Gameplay -> Delve Complete -> Results Screen -> Canopy Hub

Canopy Hub == Main Menu (same screens accessible)
```

## Screen Wireframes

### Title Screen

```
+----------------------------------------------+
|                                                |
|          ECHOES OF THE SPORE                   |
|          [subtle spore animation]              |
|                                                |
|           Press Any Key                        |
|                                                |
|  v1.0.0                       SL1C3D-L4BS     |
+----------------------------------------------+
```

Input: any key/button advances to Main Menu. Idle 60s: attract mode (camera pans zones).

### Main Menu / Canopy Hub

```
+----------------------------------------------+
| [Player Name]  [Level]  [Infection: 23%]      |
|                                                |
|  > PLAY                                        |
|    Community Tree                              |
|    Echo Codex                                  |
|    Wish Board                                  |
|    Purification Pool                           |
|    Settings                                    |
|                                                |
|  [Daily Challenge]              [Social Panel] |
+----------------------------------------------+
```

Navigation: vertical list, D-pad / arrow keys. In hub: player avatar walks between NPC stations as physical menu navigation.

### Class Select

```
+----------------------------------------------+
| SELECT YOUR CLASS                              |
|                                                |
| [Sporeling]  [Myco-Knight]  [Bloom Dancer]    |
| [Rot Priest] [Chitin Warden] [Veil Walker]    |
|                                                |
| +------------------+  +--------------------+  |
| | [Class Portrait] |  | Name: Sporeling    |  |
| |                  |  | Role: Balanced     |  |
| |                  |  | HP: 7 masks        |  |
| |                  |  | Speed: Medium      |  |
| |                  |  | Special: Spore Dash|  |
| +------------------+  +--------------------+  |
|                                                |
|              [CONFIRM]   [BACK]                |
+----------------------------------------------+
```

6 classes displayed as selectable cards. Hover shows stats panel. Confirm locks class.

### Crest Select

```
+----------------------------------------------+
| SELECT YOUR CREST                              |
|                                                |
| [7 civilization crests arranged in circle]     |
|                                                |
| Rootborn    Thinkers    Brewers                |
| Builders    Cultivators Smiths     Seekers     |
|                                                |
| Selected: The Cultivators                      |
| Bonus: Bloom (+25% healing received)           |
| Boon Pool: Cultivator boons appear more often  |
|                                                |
|              [CONFIRM]   [BACK]                |
+----------------------------------------------+
```

Crests affect boon pool weighting and grant a passive bonus tied to civilization status effect.

### Loadout (Function Selection)

```
+----------------------------------------------+
| LOADOUT: Active Functions                      |
|                                                |
| Slot 1: [Spore Burst]    Slot 2: [Root Bind]  |
| Slot 3: [Acid Spray]     Slot 4: [-----]      |
|                                                |
| Available Functions:                           |
| [Grid of unlocked functions, scrollable]       |
| [Each shows: icon, name, cooldown, mode tags]  |
|                                                |
|              [READY]   [BACK]                  |
+----------------------------------------------+
```

4 active Function slots. Drag-and-drop or select-and-assign. Ready button proceeds to matchmaking.

### Matchmaking

```
+----------------------------------------------+
|                                                |
|  SEARCHING FOR PARTY...                        |
|  [Animated mycelial tendrils connecting]       |
|                                                |
|  Region: NA-East          Ping: 32ms           |
|  Players found: 2/4       Heat: 3              |
|  Estimated wait: 00:45                         |
|                                                |
|  [Party members appear as they join]           |
|  [1: PlayerName - Sporeling]                   |
|  [2: PlayerName - Veil Walker]                 |
|  [3: Searching...]                             |
|  [4: Searching...]                             |
|                                                |
|              [CANCEL]                          |
+----------------------------------------------+
```

Auto-fill at 2+ players after 90s wait. Solo option available from Play menu.

### Loading Screen

```
+----------------------------------------------+
| [Zone artwork - full screen background]        |
|                                                |
|                                                |
|                                                |
|                                                |
| +--------------------------------------------+|
| | TIP: Fusing doubles your Silk Gauge cap.   ||
| | ===================>-------  67%           ||
| | SL1C3D-L4BS                                ||
| +--------------------------------------------+|
+----------------------------------------------+
```

See LOADING_SCREENS.md for full loading screen specification.

### Results Screen

```
+----------------------------------------------+
| DELVE COMPLETE                                 |
|                                                |
| Zone Reached: 5 - Rot Garden                  |
| Time: 34:22       Deaths: 7                   |
| Enemies Defeated: 142                          |
| Murals Discovered: 3                           |
| Infection Peak: 62%                            |
|                                                |
| Echoes Earned: 1,240                           |
| Community Tree Contribution: +340              |
|                                                |
| [CONTINUE TO HUB]   [PLAY AGAIN]              |
+----------------------------------------------+
```

### Pause Menu (In-Delve)

```
+----------------------------------------------+
| PAUSED                                         |
|                                                |
|  > Resume                                      |
|    Map                                         |
|    Codex                                       |
|    Settings                                    |
|    Abandon Delve                               |
|                                                |
| Party: 3/4   Zone: Cortex   Time: 12:45       |
+----------------------------------------------+
```

Multiplayer: pausing does NOT pause the game. Solo: game pauses.

## Navigation Rules

| Rule | Detail |
|------|--------|
| Back always available | Escape / B button returns to previous screen |
| Confirm button | Enter / A button on focused element |
| No dead ends | Every screen has an exit path |
| Menu memory | Returning to a menu restores last cursor position |
| Transition time | 0.2s crossfade between all screens |
| Input lock | 0.1s input lockout after screen transition (prevents accidental double-navigation) |

## Implementation Notes

- Menu screens are separate Unity scenes loaded additively over the hub/gameplay scene
- Navigation state managed by `MenuNavigationController` with stack-based history
- Screen transitions use `CanvasGroup` alpha tweening (DOTween, 0.2s)
- Matchmaking UI driven by `MatchmakingService` events (player joined/left, countdown)
- Results screen data populated from `DelveResultData` struct generated on delve completion
- All menu input processed by Unity Input System with UI action map
