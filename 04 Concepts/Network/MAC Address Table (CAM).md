# MAC Address Table (CAM)

A switch table mapping [[MAC Address]] to ingress port (and VLAN) used for forwarding decisions. Often stored in Content Addressable Memory (CAM) for fast lookups.

## Entries
- **MAC**: 48-bit address
- **Port**: Ingress interface
- **VLAN**: Broadcast domain identifier
- **Aging Timer**: Removes stale entries (default ~300s typical)

## How Entries Are Learned
1. Switch receives frame
2. Records source MAC + ingress port + VLAN into table
3. Uses table to forward subsequent frames to known destinations

## Forwarding Behavior
- **Known unicast**: Forward out mapped port
- **Unknown unicast**: Flood within VLAN
- **Broadcast**: Flood within VLAN
- **Multicast**: Flood (unless IGMP snooping configured)

## Operational Commands (Huawei VRP)
- `display mac-address` : Show MAC table entries
- `mac-address static` : Configure static binding (security)
- `mac-address blacklist` : Block specific MAC

## Related Concepts
- [[Ethernet Switch]]
- [[Broadcast Domain]]
- [[Collision Domain]]
- [[Ethernet]]
