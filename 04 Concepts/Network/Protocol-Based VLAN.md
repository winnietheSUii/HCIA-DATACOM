# Protocol-Based VLAN

Dynamic [[VLAN]] assignment based on [[EtherType]] field in the [[Ethernet Frame]].

## How It Works

- Switch inspects EtherType (e.g., 0x0800 = IPv4, 0x86DD = IPv6, 0x0806 = ARP)
- Frame assigned to VLAN based on protocol type
- Enables protocol-level segmentation

## Common Use Cases

- Separate IPv4 and IPv6 traffic
- Isolate legacy protocols (IPX, AppleTalk, NetBEUI)
- QoS: Voice/video protocols to dedicated VLANs

## Configuration Example (Conceptual)

```
vlan 10
  protocol-vlan ipv4

vlan 20
  protocol-vlan ipv6

interface GigabitEthernet0/0/1
  protocol-vlan enable
```

## Advantages

- Automatic segmentation by protocol
- Useful for multi-protocol networks
- No MAC or IP database to maintain

## Disadvantages

- Limited granularity (EtherType only)
- Rare in modern pure-IP networks
- Can conflict with other VLAN assignment methods

## vs. Other VLAN Methods

| Method | Basis | Use Case |
|--------|-------|----------|
| [[Port-Based VLAN]] | Port | Simple static assignment |
| [[MAC-Based VLAN]] | MAC address | User mobility |
| **Protocol-Based** | EtherType | Multi-protocol segmentation |
| [[Subnet-Based VLAN]] | IP subnet | Layer 3 segmentation |

## Related Concepts

- [[VLAN]]
- [[EtherType]]
- [[Ethernet Frame]]
- [[Port-Based VLAN]]
