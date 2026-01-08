# Inter-VLAN Routing

Routing traffic between different [[VLAN]]s using a layer 3 device (router or layer 3 switch).

## Why Inter-VLAN Routing?

- VLANs are separate [[Broadcast Domain]]s at layer 2
- Hosts in different VLANs cannot communicate without routing
- Router provides layer 3 forwarding between VLANs

## Methods

### 1. Router on a Stick

- Single physical link between switch and router
- [[Trunk Port]] carries multiple VLANs using [[802.1Q]]
- Router has subinterfaces, one per VLAN
- Legacy approach; single link can be bottleneck

**Configuration (Huawei VRP)**:
```
# Router subinterfaces
interface GigabitEthernet0/0/1.10
  dot1q termination vid 10
  ip address 192.168.10.1 255.255.255.0
  arp broadcast enable

interface GigabitEthernet0/0/1.20
  dot1q termination vid 20
  ip address 192.168.20.1 255.255.255.0
  arp broadcast enable

# Switch trunk
interface GigabitEthernet0/0/24
  port link-type trunk
  port trunk allow-pass vlan 10 20
```

### 2. Layer 3 Switch (SVI - Switched Virtual Interface)

- Switch performs routing internally
- Create VLAN interface (SVI) with IP address per VLAN
- High performance; no external router needed
- Modern recommended approach

**Configuration (Huawei VRP)**:
```
vlan 10
vlan 20

interface Vlanif10
  ip address 192.168.10.1 255.255.255.0

interface Vlanif20
  ip address 192.168.20.1 255.255.255.0
```

### 3. Multiple Physical Links

- Separate physical interface per VLAN
- Simple but wastes ports
- Rarely used (legacy)

## How It Works

1. Host A (VLAN 10) sends to Host B (VLAN 20)
2. Host A uses default gateway (router/SVI IP in VLAN 10)
3. Router receives frame, strips VLAN tag, routes packet
4. Router forwards to VLAN 20, adds VLAN 20 tag
5. Host B receives frame

## Performance Considerations

- **Router on a stick**: Limited by single trunk bandwidth
- **Layer 3 switch**: Wire-speed routing using ASICs
- **Security**: ACLs, firewalls can be applied between VLANs

## Related Concepts

- [[VLAN]]
- [[Trunk Port]]
- [[802.1Q]]
- [[Routing]]
- [[Broadcast Domain]]
