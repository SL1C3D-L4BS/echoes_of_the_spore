# UDP Transport Layer

## Why UDP (Not TCP, Not WebSocket)

| Transport | Latency | Reliability | Suitability |
|-----------|---------|-------------|-------------|
| TCP | High (head-of-line blocking) | Guaranteed | Chat, backend API |
| WebSocket | Medium | Guaranteed | Browser games |
| **UDP** | **Lowest** | **None (we add our own)** | **Real-time action games** |

## Custom Reliability Layer

Not all packets need reliability. We implement selective reliability:

| Packet Type | Reliability | Ordering | Rationale |
|------------|------------|---------|-----------|
| InputMsg | Unreliable + Redundant | Sequenced | Send last 3 ticks, drop stale |
| StateMsg | Reliable | Ordered | Full state must arrive intact |
| SyncMsg | Unreliable | Sequenced | Checksums: latest wins, old ones irrelevant |
| VoiceMsg | Unreliable | Sequenced | Real-time audio, PLC handles loss |

## Reliable Channel

For StateMsg (full state resync):
```
Sender:
  1. Assign sequence number
  2. Send packet
  3. Start 100ms retransmit timer
  4. If no ACK received: retransmit (max 5 attempts)
  5. After 5 failures: disconnect player

Receiver:
  1. Receive packet, check sequence
  2. If already received (duplicate): drop, send ACK anyway
  3. If new: process, send ACK
  4. ACK format: type(1B) + acked_seq(4B)
```

## Sequenced Channel

For InputMsg, SyncMsg, VoiceMsg:
```
Sender:
  1. Assign monotonically increasing sequence number
  2. Send packet (no retransmit timer)

Receiver:
  1. Receive packet
  2. If seq > last_received_seq: process, update last_received_seq
  3. If seq <= last_received_seq: drop (stale)
```

## Packet Header

```
All packets share a 4-byte header:
[Type: 4 bits][Flags: 4 bits][Sequence: 24 bits]

Type: INPUT=0, STATE=1, SYNC=2, VOICE=3, ACK=4, DISCONNECT=5
Flags: RELIABLE=0x1, COMPRESSED=0x2, FRAGMENTED=0x4
Sequence: 0-16,777,215 (wraps)
```

## MTU and Fragmentation

| Parameter | Value |
|-----------|-------|
| Target MTU | 1200 bytes (safe for all networks) |
| Max packet size | 1200 bytes |
| If > 1200 bytes | Fragment into multiple packets (StateMsg only) |
| Fragment reassembly timeout | 500ms |

## Socket Configuration

```csharp
var socket = new UdpClient();
socket.Client.SendBufferSize = 65536;
socket.Client.ReceiveBufferSize = 65536;
socket.Client.Blocking = false;  // Non-blocking I/O
socket.Client.SetSocketOption(
    SocketOptionLevel.Socket,
    SocketOptionName.ReuseAddress, true);
// Disable ICMP unreachable errors on Windows
const int SIO_UDP_CONNRESET = unchecked((int)0x9800000C);
socket.Client.IOControl(SIO_UDP_CONNRESET, new byte[] { 0 }, null);
```

## Bandwidth Budget

| Direction | Budget | Notes |
|-----------|--------|-------|
| Client → Server | 5 KB/s | InputMsg (3 × ~50B) × 60/s |
| Server → Client | 15 KB/s | AllInputs (4 × ~50B) × 60/s + occasional StateMsg |
| Voice (per player) | 3 KB/s | Opus 24kbps mono |
| **Total per player** | **~23 KB/s** | Well within residential bandwidth |
