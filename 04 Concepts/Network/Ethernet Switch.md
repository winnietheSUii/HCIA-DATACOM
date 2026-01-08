# Ethernet Switch

A layer 2 device that forwards frames based on [[MAC Address]] learning.

## Core Functions
- **Learning**: Builds [[MAC Address Table (CAM)]] by recording source MAC + ingress port + VLAN
- **Forwarding/Filtering**:
  - Known unicast: Forward out specific port
  - Unknown unicast: Flood to all ports in VLAN except ingress
  - Broadcast/Multicast: Flood within VLAN
- **Aging**: Removes stale MAC entries after timer
- **Loop avoidance**: Uses STP/RSTP/MSTP (not covered here)

## Benefits vs Hubs
- Each port = separate [[Collision Domain]]
- Full-duplex operation; no collisions
- Higher aggregate bandwidth
- Does **not** break [[Broadcast Domain]] (unless VLANs/Layer3)

## Switching Methods
- **Store-and-Forward**: Receives full frame, checks FCS, then forwards (default)
- **Cut-Through**: Begins forwarding after Destination MAC; lower latency, no FCS check

## Management/Verification (Huawei VRP examples)
- `display mac-address` : View MAC table
- `display interface` : Check duplex/speed/status
- `display vlan` : View broadcast domains

## Related Concepts
- [[Ethernet]]
- [[Broadcast Domain]]
- [[Collision Domain]]
- [[MAC Address Table (CAM)]]
