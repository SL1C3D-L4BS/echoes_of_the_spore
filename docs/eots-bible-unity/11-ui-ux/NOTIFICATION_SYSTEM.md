# Notification System

## Overview

The notification system handles all transient UI messages: toast notifications, milestone popups, Community Tree alerts, and Rite of Echoes announcements. Notifications never block gameplay and are queued to prevent visual overload.

## Notification Types

| Type | Priority | Duration | Position | Dismissible |
|------|----------|----------|----------|-------------|
| Toast | Low | 3s | Top-right | Auto-dismiss |
| Milestone popup | High | 10s | Center | Manual or auto |
| Community Tree alert | Medium | 5s | Top-center | Auto-dismiss |
| Rite announcement | High | 8s | Center | Manual or auto |
| Achievement | Medium | 5s | Top-right | Auto-dismiss |
| System message | Low | 4s | Top-center | Auto-dismiss |

## Toast Notifications

Small, non-intrusive messages for routine events.

### Layout

```
+----------------------------------+
| [Icon]  Toast message text here  |
|         Secondary detail text    |
+----------------------------------+
```

| Parameter | Value |
|-----------|-------|
| Width | 320px at 1080p |
| Height | 56-72px (1 or 2 lines) |
| Position | Top-right, 16px from screen edge |
| Background | Mycelium Charcoal (#1A1A2E) at 90% opacity |
| Border | 1px left accent bar, colored by category |
| Icon | 24x24, category-specific |
| Text | Space Grotesk Regular 14px, Spore White |
| Animation | Slide in from right (0.2s), hold, slide out right (0.2s) |

### Toast Categories

| Category | Left Accent Color | Icon | Example |
|----------|-------------------|------|---------|
| Loot | Necrospore Gold | Chest | "Rare Boon Fragment acquired" |
| Combat | Scorch Red | Sword | "New enemy type discovered" |
| Progress | Infection Cyan | Arrow up | "Infection Tier 3 reached" |
| Social | Bloom Green | People | "PlayerName joined your party" |
| System | Spore White | Gear | "Auto-save complete" |

### Stacking Rules

| Rule | Detail |
|------|--------|
| Max visible | 3 toasts on screen simultaneously |
| Stack direction | Newest on top, older toasts shift down |
| Overflow | Queued. Next toast appears when a slot opens. |
| Same-type merging | Repeated identical toasts merge with counter (e.g., "x3") |
| Max queue depth | 10. Beyond 10, oldest queued toast is dropped. |

## Milestone Popups

Large, celebratory notifications for significant events.

### Layout

```
+----------------------------------------------+
|                                                |
|  [Large Icon]                                  |
|                                                |
|  MILESTONE REACHED                             |
|  Branch: Cultivator - Bloom Boost              |
|  Effect: +10% healing in Rot Garden            |
|                                                |
|  Top Contributors:                             |
|  1. PlayerName (2,400)                         |
|  2. PlayerName (1,800)                         |
|  3. PlayerName (1,200)                         |
|                                                |
|  [CELEBRATE]              [DISMISS]            |
+----------------------------------------------+
```

| Parameter | Value |
|-----------|-------|
| Width | 500px at 1080p |
| Position | Center screen |
| Background | Mycelium Charcoal with 2px border (civilization color) |
| Backdrop | Full-screen Void Black at 50% opacity |
| Animation | Scale from 0.9 to 1.0 + fade in (0.3s) |
| Audio | Celebration fanfare (unique per milestone type) |
| Auto-dismiss | 10 seconds |

### Deferral Rules

| Context | Behavior |
|---------|----------|
| In combat | Deferred. Toast-only summary shown. Full popup on room clear. |
| In boss fight | Deferred until boss defeated or player dies. |
| In boon selection | Deferred until selection complete. |
| In menu | Toast shown over menu. Full popup on menu close. |
| In hub | Immediate display. |

## Community Tree Alerts

Medium-priority notifications for server-wide tree events.

### Layout

```
+----------------------------------------------+
| [Tree Icon]  The Canopy grows...               |
|              Cultivator Branch: 68% complete   |
+----------------------------------------------+
```

| Parameter | Value |
|-----------|-------|
| Width | 400px |
| Position | Top-center, below title safe area |
| Duration | 5s |
| Animation | Fade in (0.3s), hold, fade out (0.3s) |
| Frequency cap | Max 1 tree alert per 60 seconds |

### Alert Triggers

| Trigger | Message |
|---------|---------|
| Branch progress (10% increments) | "Cultivator Branch: 40% complete" |
| Branch milestone | Triggers full Milestone Popup instead |
| New branch unlocked | "New branch available: Forge Rekindling" |
| Community event start | "A Rite of Echoes has been called" |

## Rite of Echoes Announcements

High-priority notifications for competitive event lifecycle.

### Pre-Rite Announcement

```
+----------------------------------------------+
|                                                |
|  A RITE OF ECHOES BEGINS                       |
|  Mode: Spore Clash (4v4)                       |
|  Starting in: 5:00                             |
|                                                |
|  [JOIN QUEUE]              [DISMISS]           |
+----------------------------------------------+
```

| Parameter | Value |
|-----------|-------|
| Trigger | Server broadcasts Rite start 5 minutes before |
| Display duration | 8s (or until dismissed) |
| Repeat | Reminder at 2 minutes and 30 seconds |
| Position | Center screen |
| Audio | Horn call stinger (1.5s) |

### Rite Results

```
+----------------------------------------------+
|  RITE COMPLETE                                 |
|  Result: VICTORY                               |
|  Your Score: 12 kills / 3 deaths               |
|  Echoes Earned: 680                            |
|  Rank Change: Silver II -> Silver III          |
+----------------------------------------------+
```

## Notification Queue System

### Queue Architecture

```
NotificationEvent -> NotificationQueue (priority sorted)
  -> ContextFilter (defer if in combat/boss/etc.)
    -> SlotManager (max visible per type)
      -> Renderer (position, animate, display)
        -> Dismiss (auto-timer or manual input)
```

### Priority Resolution

| Priority | Types | Behavior |
|----------|-------|----------|
| 1 (Critical) | Death, Overload, disconnect | Immediate. Interrupts lower priority. |
| 2 (High) | Milestone popup, Rite announcement | Queued. Shown when no Priority 1 active. |
| 3 (Medium) | Community Tree alert, achievement | Queued behind Priority 2. |
| 4 (Low) | Toast, system message | Background. Shown in top-right stack. |

### Concurrent Display Rules

| Notification Combination | Allowed |
|--------------------------|---------|
| Multiple toasts | Yes (max 3) |
| Toast + center popup | Yes |
| Multiple center popups | No (queued, one at a time) |
| Toast + Rite announcement | Yes |
| Center popup + loading screen | No (deferred until gameplay) |

## Audio

| Notification Type | Sound | Volume |
|------------------|-------|--------|
| Toast (loot) | Coin clink | -18dB |
| Toast (combat) | Blade ring | -20dB |
| Toast (progress) | Chime up | -18dB |
| Toast (social) | Soft ping | -22dB |
| Milestone popup | Celebration fanfare | -10dB |
| Community Tree | Growth swell | -15dB |
| Rite announcement | Horn call | -8dB |
| Achievement | Achievement jingle | -12dB |

All notification audio plays on a dedicated audio bus, adjustable via Settings > Audio > Notifications.

## Implementation Notes

- `NotificationSystem` singleton manages the global notification queue
- Notifications fired via `EventBus<NotificationEvent>` with payload: type, priority, data, defer rules
- Toast rendering handled by `ToastStackRenderer` component on persistent UI canvas
- Center popups managed by `CenterPopupRenderer` with single-active-instance enforcement
- Deferral logic in `NotificationContextFilter` checks `GameStateService` for current context
- Audio playback via `NotificationAudioPlayer` with dedicated AudioMixerGroup
- Notification history stored in `NotificationLog` (last 50 notifications, viewable in pause menu)
- All notification text uses localized string table keys
