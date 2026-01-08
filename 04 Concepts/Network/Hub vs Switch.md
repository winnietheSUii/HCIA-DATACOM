# Hub vs Switch

Comparison between legacy hubs and modern Ethernet switches.

## Hub (Repeater)
- Layer 1 only; no MAC awareness
- One shared [[Collision Domain]]
- Half-duplex; uses [[CSMA-CD]]
- Floods all traffic to all ports

## Switch
- Layer 2; learns [[MAC Address]]es into [[MAC Address Table (CAM)]]
- Per-port collision domains; full-duplex
- Filters/forwards based on destination MAC; floods only when unknown/broadcast
- Can segment [[Broadcast Domain]]s using VLANs or L3 features

## Takeaway
- Switches improve efficiency, throughput, and isolation; hubs are obsolete

## Related Concepts
- [[Ethernet Switch]]
- [[Broadcast Domain]]
- [[Collision Domain]]
