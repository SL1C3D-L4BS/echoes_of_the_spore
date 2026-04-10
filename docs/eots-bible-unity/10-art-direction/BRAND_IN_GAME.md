# Brand In-Game -- SL1C3D-L4BS Branding

## Overview

SL1C3D-L4BS branding within Echoes of the Spore is minimal and integrated into the game's aesthetic. The brand should feel like it belongs in the world -- not plastered on top of it. All branding follows the Brutalist Bioluminescence visual language.

## Splash Screen

### Boot Sequence

| Step | Duration | Content |
|------|----------|---------|
| 1: Platform logo | 1.5s | Unity splash (required), platform holder logo if applicable |
| 2: SL1C3D-L4BS logo | 2.5s | Studio logo animation on Void Black background |
| 3: Echoes title | 3.0s | Game title with ambient audio fade-in |
| 4: Press any key | Indefinite | Title screen with subtle background animation |

### SL1C3D-L4BS Logo Animation

| Phase | Duration | Visual |
|-------|----------|--------|
| Fade in | 0.5s | Logo materializes as bioluminescent text, Infection Cyan glow |
| Hold | 1.5s | Logo pulses once (heartbeat rhythm) |
| Fade out | 0.5s | Logo dissolves into spore particles that drift offscreen |

### Logo Specifications

| Parameter | Value |
|-----------|-------|
| Logo font | Custom logotype (SL1C3D-L4BS wordmark) |
| Color | Spore White (#E8E4D9) with Infection Cyan (#00FFD1) glow |
| Minimum size | 200px width at 1080p |
| Clear space | 32px minimum on all sides |
| Background | Always Void Black (#0A0A0F), never transparent over gameplay |

## Watermarks on Clips

### Replay / Screenshot Watermark

When players capture screenshots or replay clips via the in-game capture system:

| Element | Specification |
|---------|--------------|
| Position | Bottom-right corner, 16px padding from edges |
| Content | SL1C3D-L4BS wordmark + "ECHOES OF THE SPORE" subtitle |
| Size | 120px wide at 1080p (scales with resolution) |
| Opacity | 40% (visible but not obstructive) |
| Color | Spore White, no glow effects |
| Toggle | Player can disable watermark in settings (default: on) |

### Replay Intro Card

Replays played back in-game or exported as video include a 1-second intro:

| Element | Specification |
|---------|--------------|
| Duration | 1.0s |
| Background | Void Black |
| Content | SL1C3D-L4BS logo (small) + "ECHOES OF THE SPORE" + player name + date |
| Animation | Fade in 0.3s, hold 0.4s, fade out 0.3s |
| Audio | None (replay audio begins after) |

## Loading Tips Style

### Loading Screen Branding

| Element | Specification |
|---------|--------------|
| Background | Zone-themed static art (random from current zone set) |
| Tip area | Bottom 20% of screen, Void Black bar at 80% opacity |
| Tip text | Space Grotesk Regular 18px, Spore White |
| Tip source | Rotates from gameplay tips, lore snippets, and community stats |
| Studio branding | Small SL1C3D-L4BS wordmark in bottom-left, 60% opacity, 80px wide |

### Tip Categories

| Category | Count | Example |
|----------|-------|---------|
| Gameplay tips | 40 | "Fusing with a partner doubles your Silk Gauge capacity." |
| Lore snippets | 30 | "The Rootborn had no word for death. They simply stopped." |
| Community stats | 10+ | "42,891 spores have fused today across all servers." |
| Seasonal | Variable | Event-specific tips, rotated by server config |

### Tip Rotation Rules

| Rule | Detail |
|------|--------|
| No repeat | Tips track per-session history, no repeats until all shown |
| Weighting | New tips (added in patches) get 2x weight for 2 weeks |
| Loading duration | If load < 2s, tip still shows for minimum 2s (artificial hold) |
| Long load | Tip rotates every 8s if loading exceeds 8s |

## Credits Sequence

### Structure

| Section | Duration | Content |
|---------|----------|---------|
| 1: Opening | 5s | "ECHOES OF THE SPORE" title, studio logo |
| 2: Core team | 60s | Name + role, scrolling, grouped by department |
| 3: Extended team | 30s | Additional contributors, contractors, testers |
| 4: Special thanks | 15s | Community, inspirations, families |
| 5: Legal | 10s | Licenses, middleware credits, copyright notice |
| 6: Closing | 5s | Mycelium Voice final line + studio logo + spore drift |

### Visual Style

| Parameter | Value |
|-----------|-------|
| Background | Slowly scrolling parallax of all 8 zones (2 minutes total loop) |
| Text | Space Grotesk Regular 20px, Spore White, center-aligned |
| Section headers | Space Grotesk Bold 28px, Necrospore Gold |
| Scroll speed | 40px/s (adjustable: hold Up to speed up 2x, Down to slow 0.5x) |
| Music | Credits theme (unique composition, 2:30 length, loops if credits exceed duration) |

### Interactive Elements

| Element | Detail |
|---------|--------|
| Skip | Hold Escape/Start for 2s to skip to end |
| Pause | Any button press pauses scroll |
| Speed control | Up arrow / D-pad Up: 2x speed. Down: 0.5x speed. |
| Post-credits | Return to main menu. Codex entry "Credits" unlocked. |
| Developer commentary | If Codex "Echoes" category complete: commentary text appears beside credits |

### Community Credits

| Feature | Detail |
|---------|--------|
| Top contributors | Top 10 Community Tree contributors listed in Special Thanks |
| Updated | Community credits refreshed each major patch |
| Opt-in | Players must opt-in to public credit listing in settings |

## In-Game Brand Placement Rules

| Rule | Detail |
|------|--------|
| No fourth-wall breaking | SL1C3D-L4BS branding never appears in the game world (no logos on walls, no meta-references) |
| Loading only | Studio branding visible only during loading, boot, credits, and capture overlays |
| Player control | Watermarks are toggleable. Loading tip branding is permanent. |
| Third-party | No third-party branding within the game under any circumstances |
| Streaming friendly | All in-game music and branding elements are DMCA-safe for content creators |

## Implementation Notes

- Splash screen sequence managed by `BootSequenceController` in the boot scene
- Logo animation uses a Timeline asset with VFX Graph particle dissolve
- Watermark overlay rendered by `CaptureWatermarkSystem` on screenshot/replay export
- Loading tips loaded from `LoadingTipDatabase` ScriptableObject, filtered by category and session history
- Credits driven by a Timeline asset with text scroll track and parallax camera track
- Community credits fetched from server API on credits scene load, cached locally
- All branding assets stored in `Assets/_Project/Branding/` (logo variants, watermark sprites, credit data)
