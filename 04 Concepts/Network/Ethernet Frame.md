# Ethernet Frame

Standard layer 2 PDU used by [[Ethernet]]. Provides source/destination MAC addressing and error detection.

## Fields
1. **Preamble** (7 bytes): Clock sync
2. **SFD** (Start Frame Delimiter, 1 byte): Marks frame start (10101011)
3. **Destination MAC** (6 bytes)
4. **Source MAC** (6 bytes)
5. **Type/Length** (2 bytes): EtherType (>=0x0600) or length (<=1500)
6. **Payload** (46-1500 bytes): Encapsulated data (e.g., IPv4/IPv6/ARP)
7. **FCS** (Frame Check Sequence, 4 bytes): CRC-32 error detection

## Size Rules
- **Minimum frame size**: 64 bytes (ensures collision detection on legacy CSMA/CD)
- **Maximum payload**: 1500 bytes (standard MTU)
- **Jumbo frames**: Vendor-specific >1500 bytes (common 9000 bytes)
- **Padding**: Added if payload <46 bytes

## Address Types
- **Unicast**: I/G bit = 0, single destination
- **Multicast**: I/G bit = 1 (except all-ones broadcast)
- **Broadcast**: FF:FF:FF:FF:FF:FF (flooded within broadcast domain)

## Related Concepts
- [[MAC Address]]
- [[Ethernet]]
- [[CSMA-CD]]
- [[Encapsulation]]
