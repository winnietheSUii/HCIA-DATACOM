# Frame vs Packet

- **Frame**: Layer 2 PDU (e.g., [[Ethernet Frame]]) containing MAC addresses and FCS
- **Packet**: Layer 3 PDU (e.g., IPv4/IPv6) containing IP headers and payload

## Encapsulation Path
Application Data → L4 Segment → L3 Packet → L2 Frame → Bits

## Why It Matters
- Switches forward frames (L2)
- Routers forward packets (L3)

## Related Concepts
- [[Encapsulation]]
- [[Ethernet Switch]]
- [[Internet Protocol]]
