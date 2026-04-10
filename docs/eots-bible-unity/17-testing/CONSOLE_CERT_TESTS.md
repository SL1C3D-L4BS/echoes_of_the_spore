# Console Certification Tests

## Overview

Console certification (cert) requires passing platform-specific technical requirements
before a game can be published on PS5, Xbox, or Switch 2. These tests are in addition
to all standard QA.

## Platform Certification Requirements

### Sony PS5 (TRC - Technical Requirements Checklist)

| Requirement | TRC ID | Test | Status |
|------------|--------|------|--------|
| System software compatibility | R001 | Boot on latest firmware | Required |
| User sign-in | R002 | Handle signed-out user | Required |
| Controller assignment | R003 | Correct player mapping (1-4) | Required |
| Suspend/resume | R004 | PS button suspend + resume, no state loss | Required |
| Error handling | R005 | Network disconnect → graceful recovery | Required |
| Save data | R006 | Save/load with correct user binding | Required |
| Trophy support | R007 | All trophies achievable, correct types | Required |
| Network features | R008 | PSN integration, online features | Required |
| Content rating | R009 | Matches IARC/PEGI/ESRB submission | Required |
| 4K support | R010 | Render at 3840x2160 or upscale | Required |
| HDR support | R011 | Correct HDR metadata + tone mapping | Recommended |
| DualSense features | R012 | Haptic feedback implementation | Recommended |
| Activity cards | R013 | PS5 activity card support | Recommended |
| Accessibility | R014 | Subtitle size, contrast, remapping | Required |

### Microsoft Xbox (XR - Xbox Requirements)

| Requirement | XR ID | Test | Status |
|------------|-------|------|--------|
| Quick Resume | XR-001 | Instant resume from suspend | Required |
| Smart Delivery | XR-002 | Correct build per console variant | Required |
| User profile | XR-003 | Correct gamertag display | Required |
| Achievements | XR-004 | All achievements unlockable | Required |
| Xbox Live | XR-005 | Online multiplayer via Xbox Live | Required |
| Cloud saves | XR-006 | Xbox cloud save integration | Required |
| Accessibility | XR-007 | Text-to-speech, narrator | Required |
| Series S support | XR-008 | Runs at 1080p60 on Series S | Required |
| Series X support | XR-009 | Runs at 4K60 on Series X | Required |
| Game Pass support | XR-010 | Correct licensing/entitlement | If applicable |
| Cross-play | XR-011 | Cross-network play compliance | If applicable |

### Nintendo Switch 2 (Lotcheck)

| Requirement | LC ID | Test | Status |
|------------|-------|------|--------|
| Docked/handheld transition | LC-001 | Seamless switch, no crash | Required |
| Controller modes | LC-002 | Pro Controller, Joy-Con, handheld | Required |
| Sleep/wake | LC-003 | Sleep + wake without state loss | Required |
| Error messages | LC-004 | Nintendo-standard error dialogs | Required |
| Save data | LC-005 | Correct save data management | Required |
| Online play | LC-006 | Nintendo Switch Online integration | Required |
| Performance | LC-007 | No drops below 20fps for > 3s | Required |
| Language support | LC-008 | All supported languages display correctly | Required |
| Touch screen | LC-009 | Touch UI in handheld (if applicable) | Recommended |

## Cert Test Automation

| Test Category | Automatable | Method |
|-------------|------------|--------|
| Boot/shutdown cycle | Yes | Script: boot → menu → play → quit × 100 |
| Suspend/resume | Partial | Dev kit API + manual verification |
| Controller disconnect/reconnect | Yes | Dev kit API simulate disconnect |
| Network disconnect/reconnect | Yes | Network condition simulator |
| Save data corruption recovery | Yes | Corrupt save file, verify graceful handling |
| Trophy/achievement trigger | Yes | Bot plays through unlock conditions |
| Memory limit compliance | Yes | Profile during worst-case scenario |
| Frame rate compliance | Yes | Continuous profiling during playthrough |
| Language switching | Yes | Cycle all languages, screenshot comparison |

## Pre-Cert Checklist (Internal)

Run before each submission to platform holder:

| Check | Pass Criteria |
|-------|--------------|
| Full playthrough (all zones) | No crashes, no soft-locks |
| 100 boot/shutdown cycles | Zero crashes |
| 50 suspend/resume cycles | Zero state loss |
| Network disconnect during every game state | Graceful recovery |
| Remove controller during gameplay | Pause menu shown |
| All text fits UI bounds (all languages) | No overflow/truncation |
| All trophies/achievements trigger correctly | 100% verified |
| Save data survives power loss simulation | Data intact |
| Memory never exceeds platform limit | PS5: < 5GB, Xbox: < 5GB, Switch: < 3GB |
| Frame rate holds 60fps (30fps Switch) | < 1% of frames below target |
| Load times within platform guidelines | See PERFORMANCE.md |
| Age rating content matches submission | Manual review |

## Cert Submission Timeline

| Step | Duration | Owner |
|------|----------|-------|
| Internal pre-cert | 2 weeks | QA Lead |
| Fix pre-cert issues | 1 week | Dev team |
| Submit to platform | 1 day | Producer |
| Platform review | 5-15 business days | Platform holder |
| Address feedback | 1 week (if needed) | Dev team |
| Re-submit (if needed) | 1 day + 5-15 days review | Producer |
| Certification granted | — | Platform holder |

## Common Cert Failures

| Issue | Platform | Prevention |
|-------|----------|-----------|
| Crash on controller disconnect | All | Test every frame state |
| Memory spike during scene transition | Switch | Profile transitions |
| Missing error dialog on network loss | All | Standardized error handling |
| Save corruption on power loss | All | Atomic save writes |
| Trophy/achievement not unlocking | PS5/Xbox | Automated trigger tests |
| Subtitle text overflow | All | Test all languages at min/max text |
| Frame drop during boss + 4 players | Switch | Profile worst-case combat |
| Incorrect user association | All | Test multi-user scenarios |
