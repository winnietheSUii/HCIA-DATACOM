# Speed and Duplex

Negotiated link characteristics on Ethernet interfaces.

## Speed
- Common: 10M, 100M, 1G, 2.5G, 5G, 10G, 25G, 40G, 100G+
- Auto-negotiation typically selects highest common speed

## Duplex
- **Full-duplex**: Simultaneous send/receive; no collisions
- **Half-duplex**: One direction at a time; uses [[CSMA-CD]]

## Duplex Mismatch Symptoms
- Late collisions, FCS errors, poor throughput
- Usually from hard-set vs auto negotiation mismatch

## Commands (Huawei VRP)
- `display interface` : View negotiated speed/duplex
- `duplex full` / `speed 1000` : Manually set

## Related Concepts
- [[Half-Duplex vs Full-Duplex]]
- [[Ethernet]]
- [[Ethernet Switch]]
