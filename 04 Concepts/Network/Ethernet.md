# Ethernet

A layer 2 technology (IEEE 802.3) for LAN communication using MAC addresses and Ethernet frames.

## Characteristics
- **Layer**: Data Link (with Physical for media/signaling)
- **Addressing**: [[MAC Address]] (48-bit)
- **Frame**: [[Ethernet Frame]] with FCS for error detection
- **Access Method**: [[CSMA-CD]] on half-duplex legacy segments; no collisions on full-duplex switched links
- **Media**: Copper (UTP/STP), fiber; speeds from 10 Mbps to 400 Gbps
- **Topology**: Star/tree via switches

## Evolution
- **Shared media**: Hubs, collisions, CSMA/CD
- **Switched Ethernet**: [[Ethernet Switch]] per-port collision domains, full duplex
- **High-speed**: Fast Ethernet (100M), GigE (1G), 10G/25G/40G/100G+

## Related Concepts
- [[LAN]]
- [[Ethernet Switch]]
- [[MAC Address Table (CAM)]]
- [[Broadcast Domain]] / [[Collision Domain]]
- [[IEEE]] 802.3
