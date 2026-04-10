# Cross-Play

## Overview

Echoes of the Spore supports cross-play between PC and consoles (Xbox, PS5, Switch). Cloud-mobile clients are spectate-only and cannot participate in gameplay sessions.

## Platform Matrix

| Platform | PvE Co-op | Rite of Echoes (PvP) | Spectate | Cloud Save Sync |
|---|---|---|---|---|
| PC (Steam) | Yes | Yes | Yes | Yes |
| PC (Epic) | Yes | Yes | Yes | Yes |
| Xbox Series X/S | Yes | Yes | Yes | Yes |
| PS5 | Yes | Yes | Yes | Yes |
| Switch | Yes | Yes (30fps cap) | Yes | Yes |
| Cloud-Mobile | No | No | Yes | Read-only |

## Input-Based Matchmaking

### Input Detection

| Method | Classification | Default Pool |
|---|---|---|
| Keyboard + Mouse | KBM | PC pool |
| Gamepad (any platform) | Controller | Console pool |
| Mixed (KBM + gamepad) | Last active input | Determined per-session |

### Pool Assignment

| Player Input | Cross-Play ON | Cross-Play OFF |
|---|---|---|
| PC-KBM | PC pool | PC-only |
| PC-Controller | Console pool | PC-only |
| Console-Controller | Console pool | Same-platform only |

- Input type locked at session start (no switching mid-delve)
- If a KBM player plugs in a controller mid-session, input type does not change until next queue

### Rite of Echoes

- Rite uses a unified pool regardless of input (all platforms, all inputs)
- Rationale: Rite is objective-based capture mode, not aim-dependent. Input advantage is minimal.
- If player complaints exceed threshold, re-evaluate with separate KBM/Controller Rite brackets

## Account and Identity

### Cross-Platform Account Linking

| Service | Purpose |
|---|---|
| Echoes Account (custom) | Central identity. All platforms link to this. |
| Steam ID | PC-Steam authentication |
| Epic Account | PC-Epic authentication |
| Xbox Live | Xbox authentication + friends |
| PSN | PS5 authentication + friends |
| Nintendo Account | Switch authentication |

### Account Linking Flow

1. Player creates Echoes Account on first launch (email + password or OAuth)
2. Platform account auto-linked on first login from that platform
3. Multiple platforms can link to same Echoes Account
4. Progression, cosmetics, and unlocks shared across linked platforms
5. Unlink via web portal (retains progress on Echoes Account, platform loses access)

### Friend System

- Echoes friend list is cross-platform (uses Echoes Account IDs)
- Platform-native friend lists visible in-game with "invite" option
- Friend list capacity: 200
- Block list shared across platforms

## Platform-Specific Considerations

### PC

| Consideration | Detail |
|---|---|
| Frame rate | Uncapped (server tick rate independent of client FPS) |
| Resolution | Arbitrary (no advantage in PvP due to fixed arena size) |
| Ultrawide | Supported but FOV capped at 110 (prevents excessive peripheral vision in Rite) |
| Modding | Client-side cosmetic mods allowed. Gameplay mods flagged by checksum validation. |

### Xbox

| Consideration | Detail |
|---|---|
| Certification | Xbox Live integration required. Achievement mapping. |
| Party chat | Xbox Party Chat disables Spore-Talk (platform limitation). Warning displayed. |
| Smart Delivery | Series X (4K/60) and Series S (1080p/60) builds |

### PS5

| Consideration | Detail |
|---|---|
| Certification | PSN integration. Trophy mapping to in-game achievements. |
| DualSense | Haptic feedback for Infection pulses. Adaptive triggers for heavy attacks. |
| Activities | PS5 Activities for zone selection and Rite scheduling |

### Switch

| Consideration | Detail |
|---|---|
| Performance | 30 fps target (docked and handheld). Reduced particle effects. |
| Resolution | 720p handheld, 1080p docked |
| Network | WiFi latency compensation: +20ms jitter buffer |
| Voice chat | Nintendo Switch Online app integration for Spore-Talk |
| Visual parity | Reduced shadow quality, simplified foliage. Gameplay identical. |

## Data Synchronization

### Cloud Save

| Parameter | Value |
|---|---|
| Save frequency | Every room transition + manual save at hubs |
| Conflict resolution | Last-write-wins with timestamp. Player warned if conflict detected. |
| Save size | ~2 MB per character |
| Storage | S3 (encrypted at rest, AES-256) |
| Sync on login | Automatic. 5s timeout before falling back to local save. |

### Progression

All progression is server-authoritative and stored in the Echoes Account:
- Character stats, inventory, Infection level
- Community Tree contributions
- Rite Elo and match history
- Codex entries and mural discoveries
- Cosmetic unlocks

Platform-specific data (achievements/trophies) synced unidirectionally: Echoes Account to platform.

## Network Considerations

| Concern | Solution |
|---|---|
| Latency variance across platforms | Server-side lag compensation (up to 150ms). Input buffer. |
| NAT traversal | All traffic through dedicated servers. No P2P. No NAT issues. |
| Platform update cadence | Hotfixes via server-side tuning. Client patches coordinated for simultaneous release. |
| Cert delays | Build buffer: submit platform builds 2 weeks before target release. PC can hotfix independently. |

## Implementation Notes

- Cross-play layer uses a custom relay service (not platform-native multiplayer APIs)
- Platform authentication handled by a unified auth gateway that issues Echoes JWT tokens
- All game traffic uses the same protocol regardless of platform (MessagePack over UDP)
- Platform-specific code isolated in `Platform/` assembly definitions (no #if preprocessor in gameplay code)
- Input abstraction layer maps all platform inputs to unified `GameInput` struct before network serialization
