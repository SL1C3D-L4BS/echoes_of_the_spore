# Audio Budgets

## FMOD Bank Size Budgets

| Bank | Max Size (Compressed) | Load Strategy | Notes |
|------|-----------------------|---------------|-------|
| Master.bank | 2 MB | Always loaded | Metadata, bus definitions |
| Music.bank | 45 MB | Stream from disk | All zone music (8 zones + hub) |
| SFX_Combat.bank | 12 MB | Always loaded | Player attacks, impacts, combos |
| SFX_Environment.bank | 8 MB | Per-zone swap | Hazards, ambient loops |
| SFX_Enemies.bank | 10 MB | Per-zone swap | Enemy sounds per zone |
| Voice_Narrator.bank | 35 MB | Stream on demand | ~406 lines, Vorbis 80% |
| Voice_Spore.bank | 3 MB | Always loaded | 50-word fungal dictionary |
| UI.bank | 2 MB | Always loaded | Menu, HUD sounds |
| **Total** | **~117 MB** | — | **Under 128 MB budget** |

## Voice Polyphony Budget

| Category | Max Voices | Typical | Steal Policy |
|----------|-----------|---------|-------------|
| Music (streaming) | 16 | 6-8 | Never steal |
| SFX (one-shot) | 32 | 12-18 | Steal quietest |
| Ambience (loops) | 8 | 4-6 | Steal furthest |
| VOIP (decoded) | 4 | 2-3 | Never steal |
| Narrator | 1 | 0-1 | Never steal |
| **Total** | **64** | **~35** | — |

## CPU Budget

| Component | Budget (ms/frame) | Thread |
|-----------|-------------------|--------|
| FMOD mixer | 0.5 | Audio thread |
| DSP effects | 0.2 | Audio thread |
| Infection DSP chain (per player) | 0.1 | Audio thread |
| Opus encode (local mic) | 0.05 | Audio thread |
| Opus decode (per remote player, x3) | 0.15 | Audio thread |
| **Total audio CPU** | **~1.0 ms** | **< 5% of frame** |

## Streaming Budget

| Resource | Buffer Size | Read Ahead |
|----------|------------|------------|
| Music stream | 32 KB per source | 200ms |
| Narrator stream | 32 KB | 200ms |
| Max concurrent streams | 4 | — |
| Disk I/O budget | 512 KB/s | — |

## Per-Platform Memory Limits

| Platform | Audio Memory Cap | Bank Strategy |
|----------|-----------------|---------------|
| PC (min spec) | 128 MB | All banks loaded |
| PS5 | 128 MB | All banks loaded |
| Xbox Series X | 128 MB | All banks loaded |
| Xbox Series S | 96 MB | Stream narrator, swap env |
| Switch 2 | 80 MB | Stream music + narrator, swap env + enemy |
| Steam Deck | 96 MB | Stream narrator, swap env |

## Asset Production Budget

| Asset Type | Count Target | Avg Duration | Total Duration |
|-----------|-------------|-------------|---------------|
| Music tracks (zone base) | 8 | 3:00 min | 24:00 min |
| Music tracks (combat layers) | 8 | 2:00 min | 16:00 min |
| Music tracks (boss themes) | 8 | 4:00 min | 32:00 min |
| Hub variations | 5 | 2:30 min | 12:30 min |
| Menu music | 1 | 2:00 min | 2:00 min |
| SFX (player) | 30 | 0.5s avg | 15s |
| SFX (combat) | 40 | 0.3s avg | 12s |
| SFX (enemies) | 60 | 0.5s avg | 30s |
| SFX (environment) | 40 | 1.0s avg | 40s |
| SFX (UI) | 25 | 0.2s avg | 5s |
| Narrator VO lines | 406 | 4.0s avg | ~27 min |
| Spore dictionary | 50 | 0.5s avg | 25s |
| Infection stingers | 6 | 2.0s avg | 12s |
| **Total unique assets** | **~687** | — | **~114 min** |

## Quality Tiers (Compression)

| Content | Source Format | Bank Format | Quality | Rationale |
|---------|-------------|------------|---------|-----------|
| Music | 48kHz/24-bit WAV | FADPCM | Lossless-ish | Quality critical |
| SFX (short) | 48kHz/16-bit WAV | Vorbis | 60% | Many assets, small |
| SFX (loops) | 48kHz/16-bit WAV | Vorbis | 70% | Longer exposure |
| Narrator VO | 48kHz/24-bit WAV | Vorbis | 80% | Clarity critical |
| Spore dictionary | 48kHz/16-bit WAV | PCM | Uncompressed | Tiny, needs DSP |
| Stingers | 48kHz/16-bit WAV | Vorbis | 70% | Short but impactful |

## Latency Budgets

| Path | Max Latency | Target |
|------|-------------|--------|
| Input → SFX playback | 16.67ms (1 frame) | < 10ms |
| Mic → local monitoring | 20ms | 15ms |
| Mic → remote playback | 80ms | 60ms |
| Zone transition → music swap | 500ms | 300ms |
| Narrator trigger → playback | 100ms | 50ms |
