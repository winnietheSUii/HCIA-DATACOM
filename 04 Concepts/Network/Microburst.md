# Microburst

Short-lived burst of traffic that temporarily exceeds egress buffer capacity on a switch, causing drops.

## Causes
- Many senders converging on one egress port
- Bursty applications
- Fan-in topologies

## Mitigation
- Larger buffers, ECN/RED
- Rate limiting or pacing at hosts
- Load balancing flows across links (LAG/ECMP)

## Related Concepts
- [[Switching Fabric]]
- [[Ethernet Switch]]
