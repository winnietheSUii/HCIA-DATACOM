# ICMP Error Detection

The process of identifying and reporting network problems using ICMP error messages.

## Error Types Detected

### Destination Unreachable
- **Host unreachable** - No route to reach the destination host
- **Network unreachable** - No route to destination network
- **Port unreachable** - Destination host received packet but port closed
- **Protocol unreachable** - Host doesn't support the protocol

### Time Exceeded
- **TTL expired in transit** - Packet [[TTL]] reached 0 while traversing network
- **TTL expired in reassembly** - Host couldn't reassemble [[Data Fragmentation|fragmented]] packet in time

### Other Errors
- **Parameter problem** - Invalid [[IPv4 Packet Format|IP header]] fields
- **Source Quench** - Router overloaded, sender should slow down

## Detection Mechanism
- Routers/hosts continuously monitor packet delivery
- When problem detected, sender of problematic packet is notified
- Error message includes original packet header for identification

## Purpose
- **Feedback to sender** - Inform about delivery issues
- **Routing table updates** - Triggers path recalculation
- **Network troubleshooting** - Helps diagnose connectivity problems