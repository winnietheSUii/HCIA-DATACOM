# Flooding

Switch behavior of sending a frame out all ports in the same VLAN except the ingress port.

## When Flooding Occurs
- Broadcast frames
- Multicast frames (unless pruned)
- Unknown unicast frames (destination not in [[MAC Address Table (CAM)]])

## Impact
- Consumes bandwidth within [[Broadcast Domain]]
- Necessary for discovery protocols (ARP, DHCP)

## Related Concepts
- [[Unknown Unicast Flooding]]
- [[Broadcast Domain]]
- [[Ethernet Switch]]
