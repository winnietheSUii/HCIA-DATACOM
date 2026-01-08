# Broadcast Domain

A logical layer 2 boundary within which broadcast frames are forwarded to all nodes.

## Characteristics
- Broadcast MAC: FF:FF:FF:FF:FF:FF
- Broadcasts flood to all ports in the same VLAN/segment
- [[Ethernet Switch]] does **not** break broadcast domains by default
- Routers (or Layer 3 switches) break broadcast domains
- VLANs create separate broadcast domains on a switch

## Impact
- Large broadcast domains increase unnecessary traffic
- Size affects ARP, DHCP, and unknown unicast flooding scope

## Related Concepts
- [[Collision Domain]]
- [[Ethernet Frame]]
- [[Ethernet Switch]]
- VLAN
