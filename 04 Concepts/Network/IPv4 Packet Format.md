# IPv4 Packet Format

The structure of an IPv4 data packet, which includes the header and payload.

## Main Components
- **Header** - Contains control information (20-60 bytes minimum)
  - Source & Destination IP addresses
  - [[TTL]] - Controls packet lifespan
  - [[Protocol]] - Identifies upper layer protocol (TCP, UDP, ICMP)
  - Flags & Fragment offset
  - Checksum for error detection
- **Payload** - The actual data being transmitted