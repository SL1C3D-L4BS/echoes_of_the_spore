# Anti-Cheat

## Philosophy

Server-authoritative architecture is the primary anti-cheat. The client is never trusted for game state. All combat resolution, loot drops, and progression happen server-side.

## Server-Authoritative Validation

### What the Server Owns

| System | Authority |
|---|---|
| HP / damage calculation | Server only. Client displays result. |
| Loot generation | Server rolls. Client receives item data. |
| Infection changes | Server calculates. Client reads. |
| Position (final) | Server validates. Client predicts. |
| Ability cooldowns | Server tracks. Client mirrors for UX. |
| Spore Gauge | Server increments. Client displays. |
| Fusion state machine | Server transitions. Client animates. |

### What the Client Sends

Only raw input: movement direction, button presses, aim vector, timestamps. No derived state.

## Input Validation

| Check | Threshold | Action |
|---|---|---|
| Input rate | Max 60 inputs/s (1 per tick) | Drop excess inputs, log warning |
| Input rate (sustained) | >45 inputs/s for 10s | Flag for review |
| Movement speed | Max 1.2x expected speed per tick | Rubber-band to last valid position |
| Ability spam | Faster than cooldown allows | Reject input, no server-side effect |
| Aim snap | >180 degrees between consecutive ticks | Log + flag (possible aimbot) |
| Teleportation | >5 units between consecutive ticks without dash/ability | Reject, rubber-band, flag |

## Checksum Verification

### Asset Checksums

- Client computes SHA-256 of critical asset bundles on launch
- Checksums sent to server during LOADING state
- Server compares against known-good manifest
- Mismatch: client cannot join session (error: ASSET_INTEGRITY_FAILURE)

### State Checksums

- Every 300 ticks (5s), client sends CRC-32 of its local game state snapshot
- Server compares against authoritative state
- Minor drift (< 3 fields): silent correction via state sync
- Major drift (>= 3 fields): full state resync + flag

## Replay Validation

- Server records all inputs for every session
- Flagged sessions queued for deterministic replay
- Replay server runs headless simulation with recorded inputs
- If replay diverges from recorded outcomes: confirmed cheat, auto-ban

## Detection Tiers

| Tier | Trigger | Response |
|---|---|---|
| 0 - Noise | Occasional input spike, minor desync | Log only |
| 1 - Suspicious | 3+ flags in single session | Shadow flag (monitoring escalated) |
| 2 - Probable | 5+ flags across 3 sessions | 24-hour soft ban (PvE only, no Rite access) |
| 3 - Confirmed | Replay validation failure or manual review | Permanent ban |

## Ban System

| Ban Type | Duration | Scope | Appeal |
|---|---|---|---|
| Soft ban | 24 hours | No Rite access, no matchmaking (solo only) | Automatic expiry |
| Hard ban | 7 days | No online access | Email appeal within 48h |
| Permanent ban | Indefinite | Account locked | Single appeal review |
| Hardware ban | Indefinite | Machine-level (HWID) | No appeal for repeat offenders |

### HWID Collection

- Collected: CPU ID, GPU ID, motherboard serial, MAC address hash
- Stored as salted SHA-256 (raw values never stored)
- Used only for ban enforcement after Tier 3 confirmation
- GDPR/CCPA: disclosed in privacy policy, deletable on account termination

## Memory Protection

- Critical game state structures use encrypted memory (XOR with rotating key)
- Pointer obfuscation for HP, Infection, position, inventory
- Periodic integrity checks on encrypted values (every 600 ticks / 10s)
- Detection of debugger attachment: client refuses to connect to online sessions

## Reporting System

| Feature | Detail |
|---|---|
| Player reports | In-session and post-match. Categories: cheat, grief, harassment. |
| Report threshold | 3 unique reports in 24h triggers staff review |
| False report penalty | Repeated false reports reduce reporter's report weight |
| Replay attachment | Reporter can flag a timestamp; replay clipped for review |

## Rite-Specific Anti-Cheat

- Stricter input validation (aim snap threshold reduced to 120 degrees)
- All Rite matches recorded and stored for 30 days
- Elo manipulation detection: win trading flagged if same players repeatedly face each other with alternating results
- Boosting detection: significant Elo gap (>500) between party members in Rite flagged

## Implementation Notes

- Anti-cheat validation runs as middleware in the dedicated server pipeline
- Input validation is the first processing step each tick (before simulation)
- Checksum jobs run on a separate thread to avoid simulation stalls
- Ban service is a standalone microservice with its own database (not in Redis)
- Replay storage: compressed input logs in S3-compatible object storage, 90-day retention
