# Multi-layer Switch

Network switch capable of **layer 2 switching** AND **layer 3 routing**. Combines switching and routing functions in one device; enables inter-VLAN communication without separate router.

## Layer 2 vs. Layer 3 Switching

| Capability | Layer 2 Switch | Multi-layer Switch |
|-----------|----------------|-------------------|
| **MAC forwarding** | ✓ | ✓ |
| **VLAN support** | ✓ | ✓ |
| **Layer 3 routing** | ✗ | ✓ |
| **IP gateway** | ✗ | ✓ (vlanif) |
| **Routing protocols** | ✗ | ✓ (OSPF, RIP) |
| **Cost** | Lower | Higher |
| **Throughput** | High (switching only) | Very high (integrated) |

## Architecture

**Hardware**: Specialized [[ASIC]] (Application-Specific Integrated Circuit)
- **Switching fabric**: Interconnects all ports at line rate
- **Routing engine**: Parallel routing processor; doesn't bottleneck switching
- **Memory**: Large [[MAC Address Table (CAM)]] + routing table

**Result**: Routing happens at **hardware speed** (nanoseconds), not software (milliseconds).

## Interfaces

### Layer 2 Interfaces
- **Physical ports** (GigabitEthernet, etc.)
- **[[VLAN]]** assignment ([[Access Port]], [[Trunk Port]], [[Hybrid Port]])
- **[[STP]]** for loop prevention

### Layer 3 Interfaces
- **[[VLANIF Interface]]**: Virtual interface per [[VLAN]]; acts as layer 3 gateway
  - Example: vlanif 10 with IP 192.168.10.1/24 (gateway for VLAN 10)
- **Physical layer 3 interface**: Rarely used (older models)
- **Routing protocols**: OSPF, RIP, BGP run on [[VLANIF Interface]]s

## Configuration Example (Huawei)

```
# Enable layer 3
[Switch] ip routing

# Create VLANs
[Switch] vlan batch 10 20 30

# Create layer 3 gateways
[Switch] interface vlanif 10
[Switch-vlanif10] ip address 192.168.10.1 255.255.255.0

[Switch] interface vlanif 20
[Switch-vlanif20] ip address 192.168.20.1 255.255.255.0

# Assign ports to VLANs
[Switch] interface GigabitEthernet0/0/1
[Switch-GigabitEthernet0/0/1] port link-type access
[Switch-GigabitEthernet0/0/1] port default vlan 10

[Switch] interface GigabitEthernet0/0/2
[Switch-GigabitEthernet0/0/2] port link-type access
[Switch-GigabitEthernet0/0/2] port default vlan 20

# Enable routing protocol (optional)
[Switch] ospf 1
[Switch-ospf-1] area 0
[Switch-ospf-1-area-0] network 192.168.10.0 0.0.0.255
[Switch-ospf-1-area-0] network 192.168.20.0 0.0.0.255
```

## Use Cases

### Campus Network
```
┌─────────────────────────────────────────┐
│  Multi-layer Core Switch                │
│  vlanif 10 (Admin)                      │
│  vlanif 20 (Faculty)                    │
│  vlanif 30 (Students)                   │
└──┬──────────────┬───────────────┬──────┘
   │              │               │
 Access        Access          Access
 VLAN 10       VLAN 20         VLAN 30
   │              │               │
  Admin        Faculty         Student
  Subnet       Subnet          Subnet
```
- All inter-VLAN routing happens locally (fast)
- No external router needed

### Hierarchical Network
```
          ┌────────────────────┐
          │  Core Multi-layer  │
          │  Switch (L3)       │
          │  vlanif 10-30      │
          └──┬─────────┬───────┘
             │         │
          Access    Access
          Switch    Switch
          (L2 only) (L2 only)
             │         │
        ┌────┴──┐   ┌──┴────┐
     VLAN 10  VLAN 20  VLAN 20 VLAN 30
```
- Core switch routes between VLANs
- Access switches only switch (simpler)
- Hierarchical: Core handles routing; Access handles access

## Performance: Why Hardware Routing?

**Traditional Router** (CPU-based routing):
```
Packet → CPU → Routing lookup → CPU → Egress → 1-10 Mbps throughput
```
Bottleneck: CPU must process each packet (slow for many packets/VLANs).

**Multi-layer Switch** (hardware routing):
```
Packet → ASIC fabric → Routing lookup (parallel) → ASIC → Egress → 100 Gbps+ throughput
```
No bottleneck: Routing happens in parallel with switching (all hardware).

**Result**: 
- Switching + routing at **same speed** (line rate)
- Thousands of concurrent flows
- No performance penalty for inter-VLAN routing

## Inter-VLAN Routing Speed

- **[[Sub-interface]] (Router)**: CPU-based; 50-500 microseconds latency
- **Multi-layer switch**: Hardware; <1 microsecond latency
- **Throughput comparison**: Router 100 Mbps vs. Switch 100+ Gbps inter-VLAN

## Routing Table

Multi-layer switch maintains **routing table** (like router):
- **Static routes**: Manually configured
- **Dynamic routes**: Learned from [[OSPF]], [[RIP]], [[BGP]]
- **Direct routes**: Added automatically for connected [[VLANIF Interface]]s

Example:
```
Destination         Next Hop       Metric
192.168.10.0/24     vlanif 10      Direct
192.168.20.0/24     vlanif 20      Direct
10.0.0.0/8          192.168.20.10  15 (via OSPF)
```

## Comparison: Router vs. Multi-layer Switch

| Aspect | Router | Multi-layer Switch |
|--------|--------|------|
| **Layer 2 switching** | No | Yes |
| **Layer 3 routing** | Yes | Yes |
| **VLAN support** | Only via [[Sub-interface]] | Native (vlanif) |
| **Inter-VLAN speed** | Slow (CPU) | Fast (hardware) |
| **Port count** | Few (2-10) | Many (24-48+) |
| **Throughput** | 100 Mbps - 10 Gbps | 100 Gbps+ |
| **Cost** | Lower | Higher |
| **Typical role** | Edge/WAN | Core/Distribution |
| **Scaling** | Limited VLANs | 100-4000+ VLANs |

## When to Use Multi-layer Switch

✅ **Large networks**: 10+ VLANs
✅ **High throughput**: Heavy inter-VLAN traffic
✅ **Centralized routing**: All routing locally (not via external router)
✅ **Redundancy**: Multiple switches; distributed routing
✅ **Modern enterprise**: Standard architecture

❌ **Avoid for**:
- Small office (few VLANs; simpler setup with router)
- Limited budget (higher cost than pure L2 switch)
- Low-speed links (multi-layer not cost-effective)

## Related Concepts

- [[VLANIF Interface]]
- [[VLAN]]
- [[Inter-VLAN Communication]]
- [[Routing]]
- [[OSPF]]
- [[RIP]]
- [[MAC Address Table (CAM)]]
- [[Access Port]]
- [[Trunk Port]]
