# ICMP (Internet Control Message Protocol)

A [[Network Layer Protocols|network layer protocol]] that enables devices to report errors and exchange control information about network conditions.

## Key Purpose
ICMP is used for:
- **Error reporting** - Notifying about packet delivery problems
- **Diagnostics** - Testing network connectivity (ping, tracert)
- **Network management** - Reporting time exceeded, destination unreachable
- **Path discovery** - Finding best routes to destinations

## Main Characteristics
- **Encapsulated in IP packets** - ICMP messages are wrapped in [[IPv4]] packets
- **No data payload** - Carries control info, not user data
- **Uses dedicated protocol number** - Protocol field = 1 in [[IPv4 Packet Format]]
- **Stateless** - No connection establishment needed

## Common ICMP Message Types

### Query Messages (Diagnostic)
- **Echo Request/Reply (Type 8/0)** - Used by `ping` command to test connectivity
- **Timestamp** - Request/reply for timing measurements

### Error Messages (Notification)
- **Destination Unreachable (Type 3)** - Host/network/port/protocol unreachable
- **Time Exceeded (Type 11)** - [[TTL]] expired, used by `tracert` command
- **Parameter Problem** - Invalid IP header parameters
- **Source Quench** - Slow down transmission (deprecated)

## Subtopics
- [[ICMP Redirection]] - Routing optimization
- [[ICMP Error Detection]] - Identifying network problems
- [[ICMP Error Report]] - Reporting mechanism details

## Tools Using ICMP
- **ping** - Sends Echo Requests to test reachability
- **tracert** - Traces route by sending packets with increasing TTL values, waits for Time Exceeded messages