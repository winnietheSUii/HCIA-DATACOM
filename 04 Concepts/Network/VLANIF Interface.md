# VLANIF Interface

Virtual layer 3 interface on a **switch** (not router) acting as gateway for a [[VLAN]]. Modern replacement for [[Sub-interface]] (Router on a Stick); enables inter-VLAN routing on **multi-layer switches**.

## Purpose

Provide **layer 3 gateway functionality** directly on a switch without needing a separate router. Each [[VLANIF Interface]] represents one [[VLAN]]; switch routes between VLANs in hardware (fast layer 3 switching).

## Key Distinction

| Device                 | Method                                 | Architecture                                             |
| ---------------------- | -------------------------------------- | -------------------------------------------------------- |
| **Router**             | [[Sub-interface]] or native interfaces | Dedicated routing device; external to switch             |
| **Multi-layer Switch** | [[VLANIF Interface]]                   | Integrated routing; switch = router + switching combined |

## Configuration (Huawei VRP)

**Step 1**: Create [[VLAN]]
```
[Switch] vlan batch 10 20 30
```

**Step 2**: Create [[VLANIF Interface]] and assign IP
```
[Switch] interface vlanif 10
[Switch-vlanif10] ip address 192.168.10.1 255.255.255.0
[Switch-vlanif10] quit

[Switch] interface vlanif 20
[Switch-vlanif20] ip address 192.168.20.1 255.255.255.0
[Switch-vlanif20] quit
```

**Step 3**: Assign ports to [[VLAN]]s
```
[Switch] interface GigabitEthernet0/0/1
[Switch-GigabitEthernet0/0/1] port link-type access
[Switch-GigabitEthernet0/0/1] port default vlan 10
[Switch-GigabitEthernet0/0/1] quit

[Switch] interface GigabitEthernet0/0/2
[Switch-GigabitEthernet0/0/2] port link-type access
[Switch-GigabitEthernet0/0/2] port default vlan 20
[Switch-GigabitEthernet0/0/2] quit
```

**Verification**:
```
display vlanif
display interface vlanif 10
```

## Architecture: How It Works

**VLANIF = Logical interface** mapped to VLAN **broadcast domain**:

```
┌─────────────────────────────────────────────────┐
│       Multi-Layer Switch                        │
├──────────────────┬──────────────────────────────┤
│   Layer 2        │     Layer 3 (Routing)        │
│  (Switching)     │    (VLANIF Interfaces)       │
├──────────────────┼──────────────────────────────┤
│ VLAN 10 broadcast│ vlanif 10: 192.168.10.1/24   │
│ domain (ports)   │                              │
├──────────────────┼──────────────────────────────┤
│ VLAN 20 broadcast│ vlanif 20: 192.168.20.1/24   │
│ domain (ports)   │                              │
├──────────────────┼──────────────────────────────┤
│ VLAN 30 broadcast│ vlanif 30: 192.168.30.1/24   │
│ domain (ports)   │                              │
└──────────────────┴──────────────────────────────┘
```

**VLANIF Interface**:
- Has **MAC address** (usually switch's base MAC or per-interface)
- Has **IP address** (serves as gateway)
- Not tied to physical port (virtual; spans all ports in [[VLAN]])
- Participates in **dynamic routing protocols** ([[OSPF]], [[RIP]])

## Communication Flow

**Scenario**: PC1 (VLAN 10) → PC2 (VLAN 20)

1. **PC1 sends** (dest IP 192.168.20.20; ARP resolves gateway = 192.168.10.1)
2. **Switch port receives** (untagged frame; VLAN 10 assumed via [[Access Port]])
3. **Layer 2 lookup**: VLAN 10 broadcast domain; finds PC2 IP not in VLAN 10
4. **Layer 3 routing**: vlanif 10 receives; checks routing table
   - Route: 192.168.20.0/24 via vlanif 20
5. **Switch routes** (internal fabric; no external router needed)
6. **Layer 2 rebuild**: New source MAC = vlanif 20 MAC
7. **Egress**: vlanif 20 → Switch port in VLAN 20 (untagged)
8. **PC2 receives** (frame with vlanif 20 MAC as source)

## Advantages

✅ **Faster**: Routing in hardware ([[ASIC]]-based); microsecond latency
✅ **No separate router**: Integrated into switch; saves cost
✅ **Scalable**: Dozens of VLANs/[[VLANIF Interface]]s supported
✅ **No bandwidth constraint**: Not tied to single port (unlike [[Sub-interface]])
✅ **Routing protocol support**: Can run [[OSPF]], [[RIP]], [[BGP]] on [[VLANIF Interface]]s
✅ **Modern standard**: Industry-wide practice

## Disadvantages

❌ **Requires multi-layer switch**: Entry-level switches may lack layer 3 hardware
❌ **Complexity**: More config than simple L2 switching
❌ **Still requires manual VLAN assignment**: Each port must belong to VLAN
❌ **Inter-switch routing**: Different switches' VLANs need external router or [[STP]]/[[Spanning Tree]]

## When [[VLANIF Interface]] is Used

- **Enterprise networks**: Typical in campus/datacenter
- **Redundancy**: Multiple switches; VLANs span switches; central routing
- **Scaling**: Hundreds of VLANs on modern switches
- **High performance**: Hardware routing needed
- **Dynamic routing**: OSPF, RIP protocols running on switches

## Comparison: [[VLANIF Interface]] vs. [[Sub-interface]]

| Aspect | [[VLANIF Interface]] | [[Sub-interface]] |
|--------|------|------|
| **Location** | Switch | Router |
| **Binding** | Virtual; independent of port | Tied to physical port |
| **Speed** | Hardware L3 (fast) | CPU-based routing (slower) |
| **Bandwidth** | Shared, no constraint | Limited by physical port |
| **Scalability** | Excellent (50-4000+ VLANs) | Poor (5-20 [[Sub-interface]]s max) |
| **Cost** | Multi-layer switch required | Separate router needed |
| **Modern usage** | Primary method (90%+) | Legacy/small networks |

## Example: [[VLANIF Interface]] vs [[Sub-interface]]

**[[VLANIF Interface]] (Preferred)**:
```
Switch with 48 ports, 10 VLANs
- vlanif 10-19 created on switch
- All routing local to switch (fast)
- No external router needed
```

**[[Sub-interface]] (Legacy)**:
```
Router with 1 port, 10 [[Sub-interface]]s
- Router G0/0/0.10-0.0.19 created
- Single 1 Gbps port shared across 10 VLANs
- Potential bottleneck if traffic heavy
```

## Related Concepts

- [[VLAN]]
- [[Sub-interface]]
- [[Inter-VLAN Communication]]
- [[Access Port]]
- [[Routing]]
- [[Multi-layer Switch]]
- [[OSPF]]
- [[RIP]]
