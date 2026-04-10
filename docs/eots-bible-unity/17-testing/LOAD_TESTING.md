# Load Testing

## Overview

Load testing validates that server infrastructure handles expected and peak player
counts without degradation. Tests run against staging environments with simulated
clients.

## Test Scenarios

| Scenario | Simulated CCU | Sessions | Duration | Frequency |
|----------|-------------|----------|----------|-----------|
| Smoke | 32 (1 pod) | 8 | 5 min | Every deploy |
| Baseline | 200 | 50 | 15 min | Weekly |
| Target Load | 1,000 | 250 | 30 min | Bi-weekly |
| Peak | 5,000 | 1,250 | 30 min | Monthly |
| Stress | 20,000 | 5,000 | 60 min | Pre-launch |
| Endurance | 1,000 | 250 | 4 hours | Monthly |
| Spike | 0 → 5,000 in 60s | — | 10 min | Pre-launch |

## Simulated Client

Headless bot that connects via UDP and sends realistic input patterns:

```
Bot Behavior Loop:
  1. Connect to matchmaking (HTTPS)
  2. Join session (UDP handshake)
  3. Receive initial state
  4. For each tick (60 Hz):
     a. Generate input (random walk + occasional attack)
     b. Send InputMsg (with redundancy)
     c. Receive SyncMsg
     d. Verify checksum (optional)
  5. After 10-30 min: disconnect gracefully
  6. Report metrics
```

## Bot Input Profiles

| Profile | Movement | Attack Rate | Fusion | Proportion |
|---------|----------|------------|--------|-----------|
| Explorer | Random walk, frequent direction change | Low (5%) | Never | 30% |
| Fighter | Beeline to enemies | High (40%) | Rare (5%) | 40% |
| Fuser | Seek partner, fuse | Medium (20%) | Frequent (30%) | 15% |
| Idle | Stationary | None | Never | 10% |
| Stress | All buttons mashed | 100% | Every frame | 5% |

## Metrics Collected

### Server Metrics

| Metric | Source | Alert Threshold |
|--------|--------|----------------|
| CPU usage per pod | Prometheus | > 80% sustained |
| Memory per pod | Prometheus | > 3.5 GB |
| Tick processing time (P99) | Custom metric | > 4ms |
| Active sessions | Redis counter | > 90% capacity |
| Session allocation latency | Custom metric | > 500ms |

### Network Metrics

| Metric | Source | Alert Threshold |
|--------|--------|----------------|
| Bandwidth per pod | Network monitor | > 80% of 50 Mbps |
| Packet loss (server-side) | UDP stats | > 2% |
| Client RTT (P95) | Ping/Pong | > 150ms |
| Matchmaking latency (P95) | Service logs | > 10s |

### Game Quality Metrics

| Metric | Source | Alert Threshold |
|--------|--------|----------------|
| Desync rate | Telemetry | > 1% of sessions |
| Rollback rate (per second) | Telemetry | > 5 per player |
| State resync frequency | Server logs | > 1 per session |
| Graceful disconnect % | Session logs | < 95% |

## Load Test Infrastructure

| Component | Tool | Notes |
|-----------|------|-------|
| Bot orchestrator | Custom Rust binary | Spawns N bot processes |
| Bot runtime | .NET 8 console app | Lightweight, no rendering |
| Metric collection | Prometheus + Grafana | Real-time dashboards |
| Test runner | GitHub Actions (scheduled) | Automated execution |
| Environment | Staging K8s cluster | Mirrors production topology |
| Report | Auto-generated HTML | Emailed to team |

## Pass/Fail Criteria

| Metric | Pass | Marginal | Fail |
|--------|------|----------|------|
| Server CPU (P95) | < 70% | 70-85% | > 85% |
| Tick time (P99) | < 3ms | 3-5ms | > 5ms |
| Client RTT (P95) | < 100ms | 100-150ms | > 150ms |
| Desync rate | < 0.5% | 0.5-2% | > 2% |
| Matchmaking P95 | < 15s | 15-30s | > 30s |
| Memory growth (endurance) | < 5% over 4h | 5-15% | > 15% (leak) |
| Crash rate | 0% | < 0.1% | > 0.1% |
| Graceful shutdown | 100% sessions | > 95% | < 95% |

## Endurance Test Specifics

4-hour continuous test at baseline load to detect:

| Issue | Detection Method |
|-------|-----------------|
| Memory leaks | Memory growth trend line |
| Handle leaks | Open file descriptor count |
| Connection leaks | Redis connection pool size |
| GC pressure | GC pause frequency and duration |
| State corruption | Periodic checksum verification |
| Timer drift | Compare server tick count vs wall clock |

## Spike Test Profile

```
Time 0:00 - 0:30   Ramp from 0 to 500 CCU (gradual)
Time 0:30 - 1:00   Hold at 500 CCU
Time 1:00 - 1:30   Spike to 5,000 CCU (instant)
Time 1:30 - 5:00   Hold at 5,000 CCU
Time 5:00 - 6:00   Drop to 500 CCU (instant)
Time 6:00 - 8:00   Hold at 500 CCU (verify recovery)
Time 8:00 - 10:00  Ramp down to 0
```

Expected auto-scaling response: pods scale from 2 to ~157 within 3 minutes.

## Report Template

```
EotS Load Test Report
Date: YYYY-MM-DD
Scenario: [Scenario Name]
Duration: [Duration]
Peak CCU: [N]

Server Performance:
  CPU P50/P95/P99: __% / __% / __%
  Memory P95: __ MB
  Tick time P95/P99: __ms / __ms

Network:
  RTT P50/P95: __ms / __ms
  Packet loss: ___%
  Bandwidth peak: __ Mbps

Game Quality:
  Desync rate: ___%
  Rollback rate: __ /s/player
  Matchmaking P95: __s

Verdict: PASS / MARGINAL / FAIL
Action items: [List]
```
