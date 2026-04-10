# Spore Pass (Monthly Subscription)

## Overview

The Spore Pass is a $4.99/month optional subscription that enhances Community Tree contribution and grants cosmetic perks. It explicitly does NOT gate any gameplay content, mechanics, or progression advantage beyond contribution speed.

## Core Benefits

| Benefit | Detail | Gameplay Impact |
|---------|--------|----------------|
| 2x Community Tree contribution | Echoes contributed to tree count double | Faster server progression (communal, not personal) |
| Weekly branch vote | Vote on priority branch each week | Influence, not control (1 vote per player) |
| Exclusive border | "Spore Patron" animated border | Cosmetic only |
| Monthly cosmetic drop | 1 random cosmetic item (Uncommon+) | Cosmetic only |
| Profile badge | Spore Pass icon on player card | Cosmetic only |

## What Spore Pass Does NOT Provide

| Excluded Benefit | Rationale |
|-----------------|-----------|
| Bonus Echoes earned | No personal economy advantage |
| Bonus XP (Battle Pass) | Battle Pass progression unaffected |
| Bonus drop rates | No loot advantage |
| Exclusive gameplay content | No gated mechanics, Functions, Crests, or zones |
| Priority matchmaking | No queue advantage |
| Extra boon choices | No run advantage |
| Reduced Infection rate | No survival advantage |
| Extra Function slots | Gated by Community Tree, not subscription |

## Pricing and Billing

| Parameter | Value |
|-----------|-------|
| Monthly price | $4.99 USD |
| Billing cycle | 30 days from purchase date |
| Auto-renewal | Yes (opt-out available) |
| Free trial | None |
| Annual option | $49.99/year (2 months free, ~$4.17/mo) |
| Family/group plan | Not available |
| Platform parity | Same price on all platforms |

## 2x Contribution Detail

| Scenario | Without Spore Pass | With Spore Pass |
|----------|-------------------|----------------|
| Run earns 200 Echoes, contributes all | 200 to tree | 400 to tree |
| Run earns 500 Echoes at Heat 20 (2.0x) | 1,000 to tree | 2,000 to tree |
| Late-joiner bonus (2x) stacks | 1,000 base | 2,000 (multiplicative) |

The 2x multiplier is applied at contribution time, not at earn time. The player's personal Echo balance is unaffected.

## Weekly Branch Vote

| Parameter | Value |
|-----------|-------|
| Vote window | Monday 00:00 UTC to Sunday 23:59 UTC |
| Votes per player | 1 (equal weight regardless of contribution) |
| Minimum voters for activation | 10 Spore Pass holders must vote |
| Priority branch effect | Selected branch receives 1.5x all contributions for that week |
| Result announcement | Monday 00:01 UTC, server-wide notification |
| Tie resolution | Alphabetical branch order |
| Vote change | Can change vote once during the week |

### Vote Impact Example

| Branch | Base Contributions (Week) | With Priority (1.5x) |
|--------|--------------------------|---------------------|
| Expansion | 50,000 | 75,000 |
| Evolution | 50,000 | 50,000 (not selected) |
| Ecology | 50,000 | 50,000 |
| Infrastructure | 50,000 | 50,000 |
| Lore | 50,000 | 50,000 |

## Monthly Cosmetic Drop

| Parameter | Value |
|-----------|-------|
| Delivery | First login after billing date |
| Rarity guarantee | Uncommon or higher |
| Item pool | Recolors, emotes, canopy decorations, borders |
| Exclusions | No Legendary skins, no Animated skins, no titles |
| Duplicate protection | No duplicates until pool exhausted for that category |

### Monthly Drop Rarity Distribution

| Rarity | Chance | Equivalent Store Value |
|--------|--------|----------------------|
| Uncommon | 60% | $2.99-4.99 |
| Rare | 30% | $4.99-7.99 |
| Epic | 10% | $9.99-12.99 |

## Cancellation Policy

| Scenario | Outcome |
|----------|---------|
| Cancel mid-cycle | Benefits active until end of paid period |
| Cancel before renewal | No charge, benefits end on cycle date |
| Lapse and re-subscribe | No penalty, contribution history preserved |
| Lapsed Spore Pass border | Border unequipped automatically, re-equipped on resub |
| Lapsed vote rights | Cannot vote until resubscribed |
| Lapsed 2x contribution | Reverts to 1x immediately on cycle end |

## Spore Pass and Other Monetization

| Interaction | Behavior |
|------------|----------|
| Spore Pass + Battle Pass | Independent; both can be active simultaneously |
| Spore Pass + Store purchases | No discount or interaction |
| Spore Pass + Heat rewards | No interaction; Heat cosmetics earned normally |
| Spore Pass + Codex rewards | No interaction; Codex cosmetics earned normally |

## Community Perception Design

| Principle | Implementation |
|-----------|---------------|
| No pay-to-win | Zero gameplay advantage for subscribers |
| Visible contribution | Spore Pass holders' contributions are visibly marked on leaderboard |
| Communal benefit | 2x contribution helps the entire server, not just the subscriber |
| Transparent voting | Vote results and tallies are public |
| No shame for non-subscribers | No UI indicators that shame free players |
| Subscription value | Monthly cosmetic drop provides tangible recurring value |

## Backend Implementation

```
Supabase: spore_pass table
  - player_id (uuid)
  - status (enum: active, lapsed, cancelled)
  - start_date (timestamp)
  - current_cycle_end (timestamp)
  - total_months_subscribed (int)
  - last_cosmetic_drop (timestamp)
  - last_cosmetic_item_id (uuid)

Redis:
  - spore_pass:{player_id}:active -> boolean (TTL = cycle end)
  - votes:{server_id}:{week}:{branch} -> count
  - votes:{server_id}:{week}:voters -> set of player_ids

Payment:
  - Stripe subscription with 30-day billing cycle
  - Webhook on payment_success -> extend cycle_end
  - Webhook on payment_failed -> grace period (3 days), then lapse
```
