# VLAN (Virtual LAN)

A logical [[Broadcast Domain]] defined at layer 2 on [[Ethernet Switch]]es, isolating traffic between groups of ports without physical separation.

## Purpose

- **Broadcast containment**: Each VLAN = separate [[Broadcast Domain]]; broadcasts don't cross VLANs
- **Security segmentation**: Isolate sensitive departments/networks
- **Traffic management**: Reduce broadcast overhead; improve performance
- **Flexibility**: Logical grouping independent of physical topology
- **Multi-tenancy**: Share single physical infrastructure

## How VLANs Work

- Ports assigned to specific VLAN IDs (1-4094)
- [[MAC Address Table (CAM)]] includes VLAN ID; frames forwarded only within same VLAN
- [[Collision Domain]]: Still per-port (unchanged)
- [[Broadcast Domain]]: Per-VLAN (changed from single flat segment)

## VLAN Tagging

- **Untagged frames**: Access ports; switch adds VLAN tag internally
- **Tagged frames**: [[802.1Q]] adds 4-byte tag between source MAC and EtherType
- Tag includes VLAN ID (12 bits = 4096 VLANs, 0 and 4095 reserved)

## Port Types

### [[Access Port]]
- Belongs to one VLAN
- Sends/receives untagged frames
- Switch adds/removes tag internally
- Typical for end hosts (PCs, printers, phones)

### [[Trunk Port]]
- Carries multiple VLANs
- Sends/receives tagged frames ([[802.1Q]])
- Has **native VLAN** (untagged traffic)
- Used for switch-to-switch, switch-to-router links

### [[Hybrid Port]] (Huawei)
- Flexible; can tag/untag per-VLAN basis
- More granular than trunk
- Less common; vendor-specific

## VLAN Assignment Methods

- **[[Port-Based VLAN]]**: Static assignment to port (most common)
- **[[MAC-Based VLAN]]**: Dynamic based on source MAC
- **[[Protocol-Based VLAN]]**: Based on EtherType
- **[[Subnet-Based VLAN]]** (IP-Based): Based on source IP
- **Voice VLAN**: Special VLAN for IP phones

## Benefits

- Scalability: Reduces broadcast domain size
- Security: Layer 2 isolation
- Cost: No extra physical switches needed
- Management: Centralized, flexible
- QoS: Per-VLAN policies

## Limitations

- VLANs don't cross routers by default; need trunking and [[Inter-VLAN Routing]]
- Security: Not a substitute for firewalls; same VLAN = same trust level
- Complexity: Misconfiguration can cause black holes

## Common VLAN IDs

- **VLAN 1**: Default VLAN on most switches; management, native VLAN (often reconfigured for security)
- **VLAN 1002-1005**: Reserved for legacy protocols (Token Ring, FDDI)
- **VLAN 4094**: Often reserved for management or internal use

## Related Concepts

- [[802.1Q]]
- [[Access Port]]
- [[Trunk Port]]
- [[Hybrid Port]]
- [[Inter-VLAN Routing]]
- [[Broadcast Domain]]
- [[Ethernet Switch]]
