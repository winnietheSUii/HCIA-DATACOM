# Broadcast

A frame or packet sent to all nodes within a [[Broadcast Domain]]. In Ethernet, destination MAC FF:FF:FF:FF:FF:FF.

## Behavior
- Switches flood broadcasts to all ports in same VLAN except ingress
- Routers do not forward broadcasts by default (containment)
- Used by ARP requests, DHCP Discover/Offer, some discovery protocols

## Impact
- Excessive broadcast traffic can consume bandwidth/CPU
- Size of broadcast domain determines scope

## Related Concepts
- [[Broadcast Domain]]
- [[Ethernet Frame]]
- [[Ethernet Switch]]
