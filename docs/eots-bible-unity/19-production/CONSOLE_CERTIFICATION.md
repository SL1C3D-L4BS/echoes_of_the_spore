# Console Certification

## Certification Timeline

| Platform | First Submission | Review Period | Expected Rounds | Target Cert Date |
|----------|-----------------|--------------|-----------------|-----------------|
| PS5 (Sony) | M21 | 10-15 business days | 2-3 | M22 end |
| Xbox (Microsoft) | M21 | 5-10 business days | 1-2 | M22 mid |
| Switch 2 (Nintendo) | M21 | 10-15 business days | 2-3 | M22 end |

## Pre-Certification Preparation

### Developer Account Requirements

| Platform | Account | Cost | Lead Time |
|----------|---------|------|-----------|
| PS5 | PlayStation Partners (DevNet) | $0 (accepted) | 4-8 weeks |
| Xbox | ID@Xbox or managed partner | $0 (accepted) | 2-4 weeks |
| Switch 2 | Nintendo Developer Portal | $0 (accepted) | 6-12 weeks |

### Dev Kit Requirements

| Platform | Kit Type | Count Needed | Cost |
|----------|----------|-------------|------|
| PS5 | TOOL (dev kit) | 2 | Loaned by Sony |
| PS5 | TEST (QA kit) | 2 | Loaned by Sony |
| Xbox Series X | XDK | 2 | Loaned by MS |
| Xbox Series S | XDK | 1 | Loaned by MS |
| Switch 2 | SDEV | 2 | Loaned by Nintendo |

## Certification Checklist by Platform

### PS5 — Sony TRC Compliance

| Category | Requirements | Our Status |
|----------|-------------|-----------|
| System | Boot time < 30s, correct splash screens | Planned |
| Users | Multi-user support, PSN sign-in | Planned |
| Controller | DualSense, multiple controllers | Planned |
| Network | PSN integration, error handling | Planned |
| Save Data | User-bound saves, corruption recovery | Planned |
| Trophies | Platinum + mix of gold/silver/bronze | Planned |
| Visual | 4K output, HDR metadata | Planned |
| Audio | Tempest compatibility (N/A for 2D) | N/A |
| Accessibility | Subtitles, contrast, button remapping | Planned |
| Content | Rating matches IARC submission | Planned |

### Xbox — XR Compliance

| Category | Requirements | Our Status |
|----------|-------------|-----------|
| Identity | Gamertag display, user switching | Planned |
| Commerce | No prohibited transactions | Verified |
| Cloud | Xbox cloud save integration | Planned |
| Online | Xbox Live multiplayer compliance | Planned |
| Devices | Controller, keyboard support | Planned |
| Smart Delivery | Correct per-console builds | Planned |
| Quick Resume | State preservation on suspend | Planned |
| Accessibility | Xbox Accessibility Guidelines | Planned |
| Performance | 60fps on Series X, 60fps on Series S | Planned |

### Switch 2 — Lotcheck

| Category | Requirements | Our Status |
|----------|-------------|-----------|
| Mode Transition | Docked ↔ handheld seamless | Planned |
| Controllers | Pro Controller, Joy-Con, handheld | Planned |
| System Features | Sleep/wake, screenshot, home menu | Planned |
| Network | Nintendo Switch Online | Planned |
| Save Data | Correct management, cloud save | Planned |
| Performance | Stable frame rate (60fps target) | Planned |
| Language | All Switch-supported languages | Planned |

## Submission Build Process

```
1. Code freeze (2 weeks before submission)
2. Internal cert pre-check (1 week)
3. Fix any pre-check failures
4. Build gold candidate
5. Full regression test pass
6. Platform-specific testing (dev kit)
7. Generate submission package:
   - Game build (.pkg / .msixvc / .nsp)
   - Submission form (platform-specific)
   - Age rating certificates
   - Trophy/achievement definitions
   - Marketing assets (icons, screenshots, descriptions)
8. Upload to platform portal
9. Submit for review
```

## Age Rating

| Region | Rating Board | Target Rating | Content Descriptors |
|--------|-------------|---------------|-------------------|
| North America | ESRB | T (Teen) | Fantasy Violence, Mild Language |
| Europe | PEGI | 12 | Violence |
| Japan | CERO | B (12+) | Violence |
| Australia | ACB | M | Fantasy Violence |
| Global digital | IARC | 12+ | Violence |

## Trophy / Achievement Design

| Trophy/Achievement | Type (PS5) | Gamerscore (Xbox) | Trigger |
|-------------------|-----------|-------------------|---------|
| First Steps | Bronze | 10 | Complete Zone 1 |
| Fused for the First Time | Bronze | 10 | Complete first fusion |
| Voice of the Mycelium | Silver | 25 | Reach Infection Tier 91+ |
| Master of All | Silver | 30 | Clear a run with each class |
| Community Builder | Silver | 25 | Contribute 1000 Echoes to tree |
| Spore Maestro | Gold | 50 | Execute 50 Spore Plans |
| The Core Beckons | Gold | 50 | Defeat Zone 8 boss |
| Heat Seeker | Gold | 50 | Clear Heat 10 |
| Platinum Trophy | Platinum | — | All other trophies earned |
| **Total** | 1P + 3G + 3S + 2B | **250 GS** | — |

## Common Cert Failure Points

| Failure | Platform | Fix Effort | Prevention |
|---------|----------|-----------|-----------|
| Crash on suspend/resume | All | 1-3 days | Test 50+ suspend cycles |
| Missing error dialog | All | 1 day | Standardize error handling |
| Save data corruption | All | 2-3 days | Atomic writes + recovery |
| Trophy not unlocking | PS5 | 1 day | Automated trophy trigger test |
| Frame drops below minimum | Switch | 3-5 days | Profile on hardware weekly |
| Incorrect splash screen order | All | 0.5 day | Follow platform guidelines exactly |
| Network error not handled | All | 1-2 days | Disconnect at every state |

## Post-Certification

| Action | Timing | Owner |
|--------|--------|-------|
| Receive cert report | 5-15 BD after submission | Platform |
| Triage failures | Same day as report | Producer + Dev Lead |
| Fix and resubmit | Within 1 week | Dev team |
| Gold master finalization | After all platforms certified | Build engineer |
| Manufacturing (physical) | 3-4 weeks after gold | Publisher |
| Day-one patch preparation | During manufacturing | Dev team |
| Launch day readiness | M24 | Full team |
