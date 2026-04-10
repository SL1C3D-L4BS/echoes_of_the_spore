# 14 — NETWORKING, DETERMINISM, ROLLBACK & SYNC MODEL

## Architecture: Client-Prediction + Server-Authoritative + Rollback

- Each client runs the full ECS simulation locally.
- The server also runs the simulation authoritatively.
- Clients send inputs to server. Server broadcasts all players' inputs per tick.
- On receiving authoritative inputs that differ from predictions: rollback + resimulate.

## Tick Model
- Fixed 60 Hz simulation tick
- Input delay: 2 ticks (33 ms) configurable 1-3
- Rollback window: max 10 ticks (166 ms)
- Beyond 10 ticks: full state resync (StateMsg from server)

## Message Types
| Message | Direction | Size | Content |
|---|---|---|---|
| InputMsg | Client → Server | ~32 B | player_id, tick, InputCommand |
| SyncMsg | Server → Client | ~136 B | tick, checksum, all 4 players' inputs |
| StateMsg | Server → Client | Variable | tick, full serialized world state (resync only) |
| VoiceMsg | Peer-to-Peer | ~256 B | player_id, seq, opus_data |

## Snapshot System
- Ring buffer: 120 snapshots (2 seconds)
- Checksum: xxHash32 over serialized bytes
- Client + server compute checksums independently. Mismatch = desync flag.
- Desync recovery: server sends StateMsg, client hard-resets.

## Fusion Sync
- Fusion state transitions are deterministic from inputs (fusion_request bool in InputCommand)
- No special network messages needed — standard input sync handles it
- Rollback across fusion transitions correctly handles: timer restoration, entity creation/destruction during resimulation
- Spore Plan: PlanExecutionCommand assembled from all partners' inputs, committed atomically, executed as single deterministic event (no rollback during execution)

## Transport
- Protocol: UDP with custom reliability layer
- Packet sequencing: uint16 sequence number
- Stale packet threshold: 30 ticks (discard older)
- No retransmission for inputs (latest input replaces missing)
- Voice: separate unreliable channel, sequence-ordered, Opus PLC for lost packets
