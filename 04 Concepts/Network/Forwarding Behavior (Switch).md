# Forwarding Behavior (Switch)

How an [[Ethernet Switch]] decides where to send frames.

## Decision Logic
- **Learn** source MAC → update [[MAC Address Table (CAM)]]
- **Lookup** destination MAC in table
- **Forward** known unicast to specific port
- **Flood** unknown unicast, broadcast, and multicast (unless IGMP snooping)
- **Filter** frames back out the ingress port

## Special Cases
- **Aging**: Remove stale entries after timer
- **Static MAC**: Never aged; often for security or servers
- **MAC Move**: If same MAC seen on new port, update entry

## Related Concepts
- [[MAC Address Table (CAM)]]
- [[Data Communication on a Switch]]
- [[Broadcast Domain]]
