# Unknown Unicast Flooding

Behavior where a switch floods a unicast frame because the destination [[MAC Address]] is not in its [[MAC Address Table (CAM)]].

## When It Happens
- Initial traffic to a new host before MAC is learned
- MAC table entry aged out
- After topology change flushes MAC table

## Impact
- Increases traffic within the [[Broadcast Domain]]
- Can expose traffic to unintended hosts (security concern)

## Mitigation
- Static MAC entries for critical devices
- Port security / DHCP snooping + dynamic ARP inspection
- Keep aging timer reasonable and ensure stable topology

## Related Concepts
- [[Forwarding Behavior (Switch)]]
- [[Ethernet Switch]]
- [[Broadcast Domain]]
