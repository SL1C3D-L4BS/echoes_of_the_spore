# SUCCESS METRICS

## KPI Framework

All metrics organized by the AARRR funnel (Acquisition, Activation, Retention, Revenue, Referral).

### Acquisition Metrics

| Metric | Launch Month Target | Month 3 Target | Month 12 Target | Measurement |
|---|---|---|---|---|
| Unit Sales (cumulative) | 500K | 1.2M | 3M | Steam API + console reports |
| Wishlists (pre-launch) | 500K+ | N/A | N/A | Steamworks |
| Wishlist conversion rate | 15%+ | N/A | N/A | Week 1 sales / wishlists at launch |
| Steam page visits | 5M (launch month) | 1M/month | 500K/month | Steamworks analytics |
| Organic search volume | 100K/month | 50K/month | 30K/month | Google Trends |
| Twitch viewership (peak) | Top-50 category | Top-100 | Top-150 | TwitchTracker |

### Activation Metrics

| Metric | Target | Red Flag | Measurement |
|---|---|---|---|
| Tutorial completion rate | > 90% | < 75% | Telemetry: session_end with rooms_cleared >= 5 |
| First Fusion attempt rate | > 80% (in co-op sessions) | < 60% | Telemetry: fusion_event within first 3 sessions |
| First boss kill rate | > 70% (within 5 attempts) | < 50% | Telemetry: boss_kill for Zone 1 |
| Time to first "aha moment" | < 10 minutes | > 20 minutes | Playtest observation + survey |
| Session 2 return rate | > 75% | < 60% | Telemetry: distinct session count per player |

### Retention Metrics

| Metric | Target | Red Flag | Industry Benchmark |
|---|---|---|---|
| D1 retention | 65% | < 50% | Premium indie avg: 45% |
| D7 retention | 40% | < 25% | Premium indie avg: 20% |
| D30 retention | 25% | < 15% | Premium indie avg: 10% |
| DAU/MAU ratio (Month 3+) | 30% | < 20% | Hades: ~35% at peak |
| Median session length | 35 minutes | < 15 minutes | Roguelite avg: 25 min |
| Sessions per week (active) | 4.5 | < 2.5 | Target: "every other day" |
| Churn at Heat 0 clear | < 20% | > 35% | Critical: must hook into Heat system |

### Revenue Metrics

| Metric | Year 1 Target | Measurement |
|---|---|---|
| Gross revenue | $150M | Platform reports |
| Net revenue (post-platform fees) | $105M | Accounting |
| ARPU (avg revenue per user) | $50 | Net revenue / units sold |
| Battle Pass attach rate | 30% | Pass purchases / active players |
| Spore Pass conversion rate | 15% | Sub purchases / MAU |
| Spore Pass churn (monthly) | < 12% | Monthly unsub rate |
| Cosmetic store ARPU (spenders only) | $12/year | Store revenue / store purchasers |
| Refund rate | < 5% | Steam refund data |

### Referral Metrics

| Metric | Target | Measurement |
|---|---|---|
| Clip volume (TikTok + YouTube Shorts) | 10K clips/week (Month 1) | Social listening tools |
| "Recommend to friend" survey score | > 8.5/10 | In-game survey at D7 |
| Steam review positivity | > 80% (Very Positive) | Steamworks |
| Word-of-mouth attribution | > 40% of new installs | "How did you hear about us?" survey |
| Community Discord members | 50K (Month 1), 200K (Month 12) | Discord analytics |

## Milestone Quality Gates

| Milestone | Go/No-Go Criteria |
|---|---|
| Vertical Slice (M10) | Fusion is fun (playtest NPS > 40). 2 zones playable. No P0 bugs. |
| Alpha (M16) | 8 zones complete. Community Tree functional. Heat system works. 50 testers avg 4+ sessions/week. |
| Closed Beta (M18) | 10K players. Desync rate < 1%. Crash rate < 0.5%. Median session > 25 min. |
| Open Beta (M22) | 50K players. Matchmaking < 30s. All platforms stable. D7 retention > 30%. |
| Launch (M24) | Metacritic 82+. Steam Very Positive. Zero P0 bugs. Server infrastructure handles 100K CCU. |

## Alert Thresholds (Live)

| Metric | Warning | Critical | Auto-Response |
|---|---|---|---|
| Desync rate | > 1% | > 3% | Page on-call engineer |
| Server crash rate | > 0.1% | > 0.5% | Auto-rollback to previous build |
| Matchmaking time | > 45s | > 120s | Scale server fleet +50% |
| DAU drop (week-over-week) | > 10% | > 25% | Emergency content review meeting |
| Refund rate | > 3% | > 7% | Investigate; if bug-related, hotfix |
| Spore Pass churn spike | > 20% monthly | > 30% | Survey churned users; review value prop |
