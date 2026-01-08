# Subnet-Based VLAN

Dynamic [[VLAN]] assignment based on source IP address or subnet. Also called IP-Based VLAN.

## How It Works

- Switch inspects source IP in packet
- Matches IP against configured subnet-to-VLAN mappings
- Assigns frame to appropriate VLAN

## Configuration Example (Conceptual)

```
vlan 10
  ip-subnet-vlan 192.168.10.0 255.255.255.0

vlan 20
  ip-subnet-vlan 192.168.20.0 255.255.255.0

interface GigabitEthernet0/0/1
  ip-subnet-vlan enable
```

## Advantages

- Logical grouping by IP subnet
- Supports user mobility within subnet
- Useful for transitioning from flat to VLAN-segmented networks

## Disadvantages

- Layer 3 inspection at layer 2 (adds complexity)
- Performance overhead (deep packet inspection)
- Requires IP addresses to be assigned (DHCP dependency)
- Conflicts with [[Inter-VLAN Routing]] design

## Use Cases

- Migration scenarios: existing IP addressing scheme doesn't align with VLANs
- Guest networks with dynamic IP assignment
- Overlapping physical/logical topologies

## vs. Other VLAN Methods

| Method | Inspection Level | Flexibility | Performance |
|--------|------------------|-------------|-------------|
| [[Port-Based VLAN]] | Port | Low | High |
| [[MAC-Based VLAN]] | Layer 2 | Medium | Medium |
| [[Protocol-Based VLAN]] | Layer 2 (EtherType) | Medium | Medium |
| **Subnet-Based** | Layer 3 (IP) | High | Low |

## Related Concepts

- [[VLAN]]
- [[IPv4]]
- [[Port-Based VLAN]]
- [[Inter-VLAN Routing]]
