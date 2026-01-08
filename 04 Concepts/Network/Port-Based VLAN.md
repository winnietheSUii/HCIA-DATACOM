# Port-Based VLAN

The most common [[VLAN]] assignment method: statically assign each switch port to a VLAN.

## How It Works

- Administrator configures port with VLAN ID
- All traffic on that port belongs to that VLAN
- Simple, predictable, widely supported

## Configuration (Huawei VRP)

```
# Create VLAN
vlan 10
  description Sales Department

# Assign access port to VLAN
interface GigabitEthernet0/0/1
  port link-type access
  port default vlan 10
```

## Advantages

- Simple to configure and troubleshoot
- Predictable behavior
- No dependencies on MAC addresses or protocols

## Disadvantages

- Static; users can't move freely without reconfiguration
- Manual updates when user changes location

## vs. Dynamic VLAN Methods

| Method | Assignment Based On | Flexibility | Complexity |
|--------|---------------------|-------------|------------|
| **Port-Based** | Port number | Low | Low |
| [[MAC-Based VLAN]] | Source MAC | High | Medium |
| [[Protocol-Based VLAN]] | EtherType | Medium | Medium |
| 802.1X VLAN | Authentication | High | High |

## Related Concepts

- [[VLAN]]
- [[Access Port]]
- [[MAC-Based VLAN]]
- [[Protocol-Based VLAN]]
