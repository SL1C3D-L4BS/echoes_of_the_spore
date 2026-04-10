# Dedicated Server Architecture

## Build Options

### Option A: Unity Headless Build (Selected)

| Aspect | Detail |
|---|---|
| Runtime | Unity 6 with Netcode for GameObjects, headless mode |
| Rendering | Disabled (`-batchmode -nographics`) |
| Physics | Full simulation (server-authoritative) |
| ECS | Custom ECS for AI + ecosystem. Runs on server. |
| Pros | Single codebase. Shared simulation logic. No desync risk from separate implementations. |
| Cons | Higher per-instance memory (~300MB). Unity overhead for headless. |

### Option B: Standalone .NET (Rejected)

| Aspect | Detail |
|---|---|
| Runtime | .NET 8 console application |
| Simulation | Re-implemented game logic in pure C# |
| Pros | Lower memory (~80MB). Faster startup. Higher server density. |
| Cons | Dual codebase maintenance. Desync risk. Cannot reuse Unity physics/collision. |

**Decision:** Unity headless. The desync risk and maintenance cost of a separate .NET server outweigh the density gains. Revisit if player counts exceed 50K concurrent.

## Server Specifications

### Per-Instance Requirements

| Resource | Value |
|---|---|
| CPU | 1 vCPU (2 recommended for Zone 8 AI Director) |
| Memory | 300-400 MB |
| Network | ~50 KB/s per player (200 KB/s max per session) |
| Disk | 100 MB (binary + logs) |
| Tick rate | 60 Hz |

### Server Density

| Instance Type | Sessions per VM | Players per VM |
|---|---|---|
| c5.xlarge (4 vCPU, 8 GB) | 8-10 sessions | 32-40 players |
| c5.2xlarge (8 vCPU, 16 GB) | 18-22 sessions | 72-88 players |
| c6g.xlarge (ARM, 4 vCPU, 8 GB) | 10-12 sessions (ARM build) | 40-48 players |

## Scaling Strategy

### Horizontal Auto-Scaling

| Metric | Scale-Up Trigger | Scale-Down Trigger |
|---|---|---|
| CPU utilization | >70% avg over 3 min | <30% avg over 10 min |
| Session occupancy | >80% of capacity | <20% of capacity |
| Queue depth | >50 players waiting >60s | Queue empty for 5 min |
| Rite event | Pre-scale 2x capacity 30 min before Rite | Return to baseline 1h after Rite |

### Regional Deployment

| Region | Provider | Location | Purpose |
|---|---|---|---|
| NA-East | AWS | us-east-1 | Primary NA |
| NA-West | AWS | us-west-2 | Secondary NA |
| EU-West | AWS | eu-west-1 | Primary EU |
| EU-Central | AWS | eu-central-1 | Secondary EU |
| APAC | GCP | asia-northeast1 | APAC (GCP for latency) |
| SA | AWS | sa-east-1 | South America |

## Hosting Architecture

```
[Players] → [CloudFront/CDN] → [API Gateway (REST)]
                                      ↓
                              [Matchmaker Service]
                                      ↓
                              [Fleet Manager] → [Auto-Scaling Group]
                                      ↓               ↓
                              [Redis Cluster]    [Game Server VMs]
                                                       ↓
                                                [Unity Headless Instances]
```

### Fleet Manager Responsibilities

1. Maintain pool of warm server instances (pre-booted, waiting for sessions)
2. Assign sessions to instances based on region + load
3. Monitor instance health (heartbeat every 5s)
4. Recycle instances after 100 sessions or 24 hours (whichever first)
5. Report metrics to monitoring stack

## Warm Pool

| Parameter | Value |
|---|---|
| Warm instances per region | 10% of peak capacity or minimum 5 |
| Boot time (cold) | ~15s (Unity headless startup) |
| Boot time (warm) | <1s (already running, assign session) |
| Instance lifetime | Max 24h or 100 sessions |

## Logging and Monitoring

| System | Tool | Purpose |
|---|---|---|
| Metrics | Prometheus + Grafana | Tick rate, session count, player count, latency |
| Logs | ELK Stack (Elasticsearch, Logstash, Kibana) | Server logs, error tracking |
| Alerting | PagerDuty | Tick rate drop, instance crash, Redis connection loss |
| Tracing | OpenTelemetry | Request tracing across matchmaker → fleet → server |

### Key Metrics

| Metric | Alert Threshold |
|---|---|
| Tick rate | <55 Hz for >5s |
| Memory | >85% per instance |
| Session crash rate | >2% in 5-min window |
| Matchmaker latency | >500ms per match attempt |
| Redis latency | >10ms p99 |

## Disaster Recovery

| Scenario | Response |
|---|---|
| Single instance crash | Sessions reassigned to warm pool within 5s. Redis state preserved. |
| AZ failure | Cross-AZ failover within same region. 30s interruption. |
| Region failure | Players redirected to secondary region. 2-5 min interruption. |
| Redis cluster failure | Read replicas promote. ~10s failover. In-flight sessions may lose state. |

## Cost Estimates (Per Region, Monthly)

| Component | Estimate |
|---|---|
| Game servers (c5.xlarge x 20 avg) | $2,400 |
| Redis Cluster (r5.large x 3) | $900 |
| Matchmaker service (t3.medium x 2) | $120 |
| S3 replay storage | $50-200 (scales with players) |
| CloudFront / bandwidth | $300-800 |
| Monitoring stack | $200 |
| **Total per region** | **~$4,000-4,500** |

## Implementation Notes

- Unity headless build pipeline: separate build target in CI/CD (no asset bundles, no shaders)
- Server binary distributed as Docker container for consistent deployment
- Docker image: `ubuntu:22.04` base + Unity headless runtime + .NET 8 runtime
- Health check endpoint: HTTP GET `/health` returns session count, tick rate, uptime
- Graceful shutdown: on SIGTERM, finish current tick, save session state to Redis, notify fleet manager
