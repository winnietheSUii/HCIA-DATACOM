# EtherType

A 2-byte field in [[Ethernet Frame]]s indicating the upper-layer protocol.

## Common Values
- 0x0800 : IPv4
- 0x86DD : IPv6
- 0x0806 : ARP
- 0x8100 : 802.1Q VLAN tag (tagged frames)
- 0x8847 : MPLS unicast
- 0x8848 : MPLS multicast

## Notes
- Values < 0x0600 represent IEEE 802.3 length (not EtherType)
- Used by switches and NICs to pass payload to correct handler

## Related Concepts
- [[Ethernet Frame]]
- [[Encapsulation]]
