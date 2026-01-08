# VLAN Interface (Vlanif / SVI)

A virtual layer 3 interface on a switch representing a [[VLAN]]. Enables [[Inter-VLAN Routing]] without external router.

## Terminology

- **SVI** (Switched Virtual Interface): Cisco/industry term
- **Vlanif**: Huawei term
- **VLAN interface**: Generic term

## Purpose

- Assign IP address to VLAN (default gateway for hosts)
- Enable layer 3 routing between VLANs
- Management access to switch (e.g., Vlanif1 for VLAN 1)

## Configuration (Huawei VRP)

```
# Create VLAN
vlan 10
  description Engineering

# Create VLAN interface
interface Vlanif10
  ip address 192.168.10.1 255.255.255.0
  description Gateway for Engineering
```

## How It Works

1. Hosts in VLAN 10 use 192.168.10.1 as default gateway
2. When host sends to different subnet, frame sent to gateway MAC
3. Switch receives frame in VLAN 10
4. Vlanif10 processes packet (layer 3)
5. Switch routes to destination VLAN
6. Destination Vlanif tags frame, forwards to destination port

## Requirements

- VLAN must exist before creating Vlanif
- Layer 3 switch or multilayer switch capability
- Routing must be enabled (often default on L3 switches)

## Layer 2 vs. Layer 3 Switches

| Feature | Layer 2 Switch | Layer 3 Switch |
|---------|----------------|----------------|
| VLAN support | Yes | Yes |
| Vlanif/SVI | No | Yes |
| Routing | No | Yes (hardware-accelerated) |
| Cost | Lower | Higher |

## VLAN 1 Management

- Vlanif1 commonly used for switch management
- Assigned management IP for SSH/SNMP/Web access
- Best practice: Use dedicated management VLAN (not VLAN 1)

## Related Concepts

- [[VLAN]]
- [[Inter-VLAN Routing]]
- [[Trunk Port]]
- [[Routing]]
