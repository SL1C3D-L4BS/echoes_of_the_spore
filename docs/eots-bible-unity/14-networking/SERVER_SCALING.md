# Server Scaling

## Architecture Overview

```
                     ┌──────────────────┐
Internet ──────────→ │  Load Balancer   │
                     │  (HAProxy/NLB)   │
                     └────────┬─────────┘
                              │
              ┌───────────────┼───────────────┐
              │               │               │
        ┌─────┴─────┐  ┌─────┴─────┐  ┌─────┴─────┐
        │  Game Pod  │  │  Game Pod  │  │  Game Pod  │
        │  (8 sess)  │  │  (8 sess)  │  │  (8 sess)  │
        └─────┬─────┘  └─────┬─────┘  └─────┬─────┘
              └───────────────┼───────────────┘
                              │
                     ┌────────┴─────────┐
                     │    Redis Cluster  │
                     │  (Session State)  │
                     └────────┬─────────┘
                              │
                     ┌────────┴─────────┐
                     │    Supabase      │
                     │  (Persistent)    │
                     └──────────────────┘
```

## Server Pod Specifications

| Parameter | Value | Notes |
|-----------|-------|-------|
| Runtime | Unity Headless (IL2CPP) or .NET 8 Console | Headless preferred |
| Sessions per pod | 8 | 4 players × 8 = 32 players per pod |
| CPU per pod | 2 vCPU | Each session uses ~12% of 1 core |
| Memory per pod | 4 GB | 500 MB per session |
| Network per pod | 50 Mbps | 23 KB/s × 32 players + overhead |
| Container | Docker (linux/amd64) | K8s managed |
| Orchestrator | Kubernetes (EKS/GKE) | Auto-scaling |

## Scaling Tiers

| Tier | CCU Target | Pods Required | Monthly Cost (Est.) |
|------|-----------|--------------|-------------------|
| Dev/Test | 32 | 1 | $150 |
| Alpha | 200 | 7 | $1,050 |
| Closed Beta | 1,000 | 32 | $4,800 |
| Open Beta | 5,000 | 157 | $23,550 |
| Launch Day | 20,000 | 625 | $93,750 |
| Steady State | 5,000 | 157 | $23,550 |
| Peak Event | 50,000 | 1,563 | $234,450 |

Cost basis: $0.05/pod/hour (c5.large equivalent), 720 hrs/month.

## Auto-Scaling Policy

| Metric | Scale Up | Scale Down | Cooldown |
|--------|----------|-----------|----------|
| Pod CPU usage | > 70% for 2 min | < 30% for 10 min | 5 min |
| Session fill rate | > 75% pods full | < 25% pods full | 5 min |
| Matchmaking queue depth | > 50 waiting | < 10 waiting | 3 min |
| Min pods | 2 (always-on) | — | — |
| Max pods | 2000 | — | — |
| Scale step | +25% current | -10% current | — |

## Region Deployment

| Region | Provider | Priority | Notes |
|--------|----------|----------|-------|
| US East (Virginia) | AWS us-east-1 | Primary | Largest player base |
| US West (Oregon) | AWS us-west-2 | Primary | West coast coverage |
| EU West (Frankfurt) | AWS eu-central-1 | Primary | European players |
| Asia Pacific (Tokyo) | AWS ap-northeast-1 | Secondary | JP/KR players |
| South America (Sao Paulo) | AWS sa-east-1 | Secondary | LATAM |
| Oceania (Sydney) | AWS ap-southeast-2 | Tertiary | ANZ players |

## Matchmaking Service

Separate from game pods, runs as a stateless service:

| Parameter | Value |
|-----------|-------|
| Runtime | Rust (Axum) |
| Instances | 2-4 per region |
| Match timeout | 30s (then expand search) |
| Region preference | Lowest ping first |
| Cross-region | Allowed after 60s wait, with warning |
| Party support | 1-4 players pre-grouped |
| Skill matching | MMR range: +/- 200 initially, +50 per 10s wait |

## Session Lifecycle on Server

| State | Duration | Resources |
|-------|----------|-----------|
| Allocating | < 1s | Pod reserves session slot |
| Waiting for players | 30s timeout | Minimal (no simulation) |
| Active | 20-60 min typical | Full simulation running |
| Winding down | 10s | Save results, upload telemetry |
| Cleanup | < 1s | Release slot, destroy world |

## Redis Usage

| Key Pattern | Data | TTL |
|-------------|------|-----|
| `session:{id}:state` | Session metadata (players, zone, status) | Session lifetime |
| `session:{id}:inputs` | Input backup for desync diagnosis | 5 min rolling |
| `player:{id}:infection` | Infection level for VOIP DSP sync | 500ms refresh |
| `matchmaking:queue:{region}` | Waiting players sorted by MMR | Auto-expire 120s |
| `community_tree:state` | Global tree progress | Persistent |
| `leaderboard:{season}` | Ranked scores | Season duration |

## Health Checks

| Check | Interval | Failure Action |
|-------|----------|----------------|
| Pod liveness | 10s | Restart pod |
| Pod readiness | 5s | Remove from LB |
| Session heartbeat | 5s | Mark session unhealthy after 3 missed |
| Redis connectivity | 5s | Failover to replica |
| Matchmaking health | 5s | Route to healthy instance |

## Disaster Recovery

| Scenario | Response | RTO |
|----------|----------|-----|
| Single pod crash | K8s restarts, sessions lost (players reconnect) | 30s |
| Region outage | DNS failover to nearest region | 5 min |
| Redis primary down | Sentinel auto-failover to replica | 15s |
| Full cluster failure | Manual intervention, restore from backup | 1 hour |
