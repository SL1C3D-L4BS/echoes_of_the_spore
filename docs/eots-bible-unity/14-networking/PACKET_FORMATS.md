# Packet Formats

## Universal Packet Header (4 bytes)

```
Bits:  [3333][4444][2222 2222 2222 2222 2222 2222]
Field:  Type  Flags          Sequence (24-bit)

Type (4 bits):
  0x0 = INPUT
  0x1 = STATE
  0x2 = SYNC
  0x3 = VOICE
  0x4 = ACK
  0x5 = DISCONNECT
  0x6 = DESYNC_REPORT
  0x7 = PING
  0x8 = PONG
  0x9 = SPECTATE_STATE
  0xA-0xF = Reserved

Flags (4 bits):
  bit 0 = RELIABLE     (expects ACK)
  bit 1 = COMPRESSED   (zstd compressed payload)
  bit 2 = FRAGMENTED   (multi-packet message)
  bit 3 = Reserved
```

## InputMsg (0x0)

```
[Header:        4B]  type=0x0, flags=0x0, seq
[player_id:     1B]  0-3 (player slot)
[tick_count:    1B]  Number of InputCommands (1-3)
[InputCommand × N:]
  [tick:        4B]  Simulation tick
  [move_x:      1B]  0=left, 1=none, 2=right
  [move_y:      1B]  0=down, 1=none, 2=up
  [buttons:     2B]  Bitfield (16 flags)
  [checksum:    4B]  xxHash32 of this command

Total: 4 + 2 + (12 × N) bytes
Typical (N=3): 42 bytes
Reliability: Unreliable, Sequenced
```

## SyncMsg (0x2)

```
[Header:        4B]  type=0x2, flags=0x0, seq
[tick:          4B]  Authoritative tick number
[checksum:      4B]  xxHash32 of full world state at this tick
[player_inputs: 48B] All 4 players' InputCommands for this tick (12B × 4)

Total: 60 bytes
Reliability: Unreliable, Sequenced
Frequency: Every 4 ticks (15/s)
```

## StateMsg (0x1)

```
[Header:        4B]  type=0x1, flags=0x3 (RELIABLE | COMPRESSED), seq
[tick:          4B]  Tick of this snapshot
[entity_count:  2B]  Number of entities
[payload_len:   4B]  Compressed payload size
[payload:       var] zstd-compressed serialized world state

Uncompressed layout per entity:
  [entity_id:   4B]
  [archetype:   2B]  Component bitmask
  [components:  var] Serialized component data (deterministic order)

Typical size: 2-8 KB compressed (500 entities)
Max size: 64 KB (fragmented if > 1200B MTU)
Reliability: Reliable, Ordered
Usage: State resync only (desync recovery, late join)
```

## VoiceMsg (0x3)

```
[Header:        4B]  type=0x3, flags=0x0, seq
[player_id:     1B]  Speaker ID
[timestamp:     4B]  Audio sample timestamp (wrapping)
[frame_count:   1B]  Opus frames in packet (1-3)
[frames:]
  [frame_len:   2B]  Opus frame byte length
  [frame_data:  var] Opus-encoded audio (typ. 60-120B per frame)

Typical size (1 frame): 70-130 bytes
Max size: 270 bytes (3 frames)
Reliability: Unreliable, Sequenced
```

## ACK (0x4)

```
[Header:        4B]  type=0x4, flags=0x0, seq
[acked_seq:     4B]  Sequence number being acknowledged

Total: 8 bytes
Reliability: Unreliable (single attempt)
```

## Disconnect (0x5)

```
[Header:        4B]  type=0x5, flags=0x1 (RELIABLE), seq
[player_id:     1B]  Disconnecting player
[reason:        1B]  Reason code (see table)

Total: 6 bytes
```

| Reason Code | Meaning |
|------------|---------|
| 0x00 | Graceful quit |
| 0x01 | Timeout (no packets for 10s) |
| 0x02 | Kicked by host |
| 0x03 | Server shutting down |
| 0x04 | Version mismatch |
| 0x05 | Authentication failure |
| 0x06 | Desync unrecoverable |

## DesyncReport (0x6)

```
[Header:        4B]  type=0x6, flags=0x1 (RELIABLE), seq
[tick:          4B]  Desync tick
[client_hash:   4B]  Client's world checksum
[server_hash:   4B]  Server's checksum (from SyncMsg)
[entity_count:  2B]  Client's entity count
[system_count:  1B]  Number of system checksums following
[system_hashes: var] Per-system xxHash32 (4B × system_count)

Typical size: 50-80 bytes
```

## Ping/Pong (0x7, 0x8)

```
Ping:
[Header:        4B]  type=0x7, flags=0x0, seq
[send_time:     8B]  High-resolution timestamp (microseconds)
Total: 12 bytes

Pong:
[Header:        4B]  type=0x8, flags=0x0, seq
[echo_time:     8B]  Echoed send_time from Ping
[server_time:   8B]  Server's current timestamp
Total: 20 bytes
```

## SpectateState (0x9)

```
[Header:        4B]  type=0x9, flags=0x3 (RELIABLE | COMPRESSED), seq
[tick:          4B]  Current tick
[payload_len:   4B]  Compressed state size
[payload:       var] Same format as StateMsg payload

Frequency: 4/s (every 15 ticks)
```

## Fragmentation Protocol

For messages exceeding 1200B MTU (primarily StateMsg):

```
Fragment header (appended to each fragment):
  [message_id:  2B]  Groups fragments of same message
  [fragment_idx: 1B] Fragment index (0-based)
  [fragment_total: 1B] Total fragments in message
  [fragment_len: 2B] Payload bytes in this fragment

Reassembly timeout: 500ms
Max fragments per message: 64 (76.8 KB max message size)
Missing fragment: re-request entire message
```

## Byte Order

All multi-byte fields are **little-endian** (matches x86/ARM default).

## Compression

| Algorithm | Used For | Level | Ratio |
|-----------|---------|-------|-------|
| zstd | StateMsg, SpectateState | 3 (fast) | ~3:1 typical |
| None | InputMsg, SyncMsg, VoiceMsg, ACK | — | 1:1 |

## Bandwidth Summary

| Packet | Direction | Rate | Size | Bandwidth |
|--------|-----------|------|------|-----------|
| InputMsg | Client → Server | 60/s | 42B | 2.5 KB/s |
| SyncMsg | Server → Client | 15/s | 60B | 0.9 KB/s |
| InputRelay | Server → Client | 60/s | 168B | 10.1 KB/s |
| VoiceMsg | Peer/Relay | 50/s | 100B avg | 5.0 KB/s |
| Ping/Pong | Both | 1/s | 16B avg | 0.016 KB/s |
| StateMsg | Server → Client | Rare | 4KB avg | ~0 (burst) |
| **Total per player** | — | — | — | **~18.5 KB/s** |
