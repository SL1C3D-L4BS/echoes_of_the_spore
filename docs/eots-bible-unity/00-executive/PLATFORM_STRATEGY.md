# PLATFORM STRATEGY

## Launch Platform Sequence

| Phase | Platform | Date (Relative to Launch) | Rationale |
|---|---|---|---|
| Day 1 | Steam (PC) | L+0 | Largest indie audience, wishlist infrastructure, community tools |
| Day 1 | Epic Games Store | L+0 | Revenue share (88/12), cross-promotion |
| Day 1 | GOG | L+0 | DRM-free audience, goodwill |
| Month 3 | PS5 | L+3 months | Console certification lead time |
| Month 3 | Xbox Series X/S | L+3 months | Game Pass negotiation window |
| Month 3 | Switch 2 | L+3 months | Portable play appeal |
| Month 6 | Steam Deck (verified) | L+6 (optimization patch) | Growing portable PC market |
| Month 6 | Cloud Mobile (streaming) | L+6 | Spectate mode + light play via xCloud/GeForce NOW |

## Platform-Specific Requirements

### Steam (PC)

| Requirement | Implementation |
|---|---|
| Steam Input API | Integrated via Unity Input System Steam overlay |
| Steam Rich Presence | Show current zone, Fusion status, Heat level |
| Steam Achievements | 50 achievements mapped from Echo Codex milestones |
| Steam Trading Cards | 8 cards (one per civilization), crafted badge = exclusive emote |
| Steam Workshop | Post-launch consideration (room templates, custom Heat conditions) |
| Steam Deck compatibility | Verified: controller layout, readable at 800p, 60fps target |
| Remote Play Together | Supported for Fusion co-op (host streams to guests) |

### PlayStation 5

| Requirement | Implementation |
|---|---|
| DualSense haptics | Infection pulse mapped to adaptive triggers; boss impacts via haptic feedback |
| Activity Cards | Quick resume into last Canopy state |
| Trophies | Platinum + 50 trophies (mirrored from Steam achievements) |
| PS Plus Required | Yes (for multiplayer; solo is offline-capable) |
| Cross-play with PC | Day 1 of console launch |

### Xbox Series X/S

| Requirement | Implementation |
|---|---|
| Smart Delivery | Series X: 4K/60. Series S: 1080p/60 (reduced parallax layers) |
| Xbox Play Anywhere | Evaluate post-launch (PC + Xbox linked purchase) |
| Game Pass | Negotiate 6-month window; requires minimum guarantee vs. royalty |
| Cross-play | PC + PS5 + Xbox at console launch |
| Quick Resume | Supported (save ECS state snapshot on suspend) |

### Nintendo Switch 2

| Requirement | Implementation |
|---|---|
| Resolution | Docked: 1080p/60. Handheld: 720p/60 |
| Touch controls | Not supported (too complex for touch) |
| Local wireless | 2P local co-op via split Joy-Con (simplified controls) |
| Online requirement | Nintendo Switch Online for multiplayer |
| Performance | Reduced particle density, simplified parallax (3 layers instead of 5) |

## Cross-Play Architecture

```
         ┌──────────────┐
         │  MATCHMAKING  │
         │   SERVICE     │
         │  (Platform-   │
         │   agnostic)   │
         └──────┬───────┘
                │
    ┌───────────┼───────────┐
    │           │           │
┌───▼───┐  ┌───▼───┐  ┌───▼───┐
│  PC   │  │  PS5  │  │  Xbox │
│ Steam │  │  PSN  │  │  XBL  │
│ Epic  │  │       │  │       │
│ GOG   │  │       │  │       │
└───┬───┘  └───┬───┘  └───┬───┘
    │           │           │
    └───────────┼───────────┘
                │
         ┌──────▼───────┐
         │  DEDICATED   │
         │  SERVER      │
         │  (Platform-  │
         │   agnostic)  │
         └──────────────┘
```

**Cross-play rules:**
- All platforms connect to the same dedicated servers
- Matchmaking pools are merged by default
- Players can opt-in to "same-platform only" matchmaking
- Account linking via Supabase (platform ID mapped to EotS account)
- Community Tree progress is per-server, not per-platform

## Platform Revenue Split

| Platform | Revenue Share (Developer) | Notes |
|---|---|---|
| Steam | 70% (< $10M), 75% ($10-50M), 80% (> $50M) | Tiered; likely 75%+ for Echoes |
| Epic Games Store | 88% | Best split, smaller audience |
| GOG | 70% | Niche but loyal audience |
| PlayStation | 70% | Standard Sony split |
| Xbox | 70% (retail), Game Pass (negotiated) | Game Pass: lump sum + per-play royalty |
| Switch | 70% | Standard Nintendo split |

## Platform-Specific Performance Targets

| Platform | Resolution | FPS | Max Entities | Parallax Layers | Memory Budget |
|---|---|---|---|---|---|
| PC (min spec) | 1080p | 60 | 4096 | 5 | 4 GB |
| PC (recommended) | 1440p/4K | 60 | 4096 | 5 | 8 GB |
| PS5 | 4K | 60 | 4096 | 5 | 4 GB (of 16 GB shared) |
| Xbox Series X | 4K | 60 | 4096 | 5 | 4 GB (of 16 GB shared) |
| Xbox Series S | 1080p | 60 | 3072 | 4 | 3 GB (of 10 GB shared) |
| Switch 2 | 1080p/720p | 60 | 2048 | 3 | 2 GB |
| Steam Deck | 1280x800 | 60 | 3072 | 4 | 4 GB |

## PC Minimum Specifications

| Component | Minimum | Recommended |
|---|---|---|
| OS | Windows 10 64-bit | Windows 11 64-bit |
| CPU | Intel i5-8400 / AMD Ryzen 5 2600 | Intel i7-10700 / AMD Ryzen 7 3700X |
| RAM | 8 GB | 16 GB |
| GPU | NVIDIA GTX 1060 / AMD RX 580 | NVIDIA RTX 2060 / AMD RX 6600 |
| VRAM | 4 GB | 6 GB |
| Storage | 10 GB SSD | 10 GB NVMe SSD |
| Network | Broadband (5 Mbps+) | 25 Mbps+ |
| DirectX | 12 | 12 |
