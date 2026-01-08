# Broadcast Storm

A condition where excessive broadcast or flooded traffic overwhelms a network segment, consuming bandwidth and CPU.

## Causes
- Layer 2 loops without STP
- Misconfigured or malicious hosts generating many broadcasts
- Frequent unknown unicast flooding due to unstable MAC tables

## Effects
- High CPU on switches/hosts
- Packet loss and latency
- Network unreachability

## Mitigation
- Enable STP/RSTP/MSTP
- Use storm-control/rate-limiting on switch ports
- Reduce broadcast domain size (VLANs, routing)

## Related Concepts
- [[Broadcast Domain]]
- [[Unknown Unicast Flooding]]
- [[Ethernet Switch]]
