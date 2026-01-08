# Inter-VLAN Communication

Process enabling devices in different [[VLAN]]s to communicate through a **layer 3 device** ([[Routing]]). Without layer 3, VLANs are isolated [[Broadcast Domain]]s and cannot exchange traffic.

## Problem: VLAN Isolation

- **Same [[VLAN]]**: Layer 2 switching (MAC-based forwarding); no router needed
- **Different [[VLAN]]s**: Layer 2 cannot forward (BPDUs, MAC lookups fail); **requires [[Routing]]**

[[VLAN]] is a **layer 2 boundary**; crossing it requires layer 3 routing.

## Solution: Router Interface on Each VLAN

Router with **one interface per [[VLAN]]**:
- **VLAN 10 interface**: IP 192.168.10.1/24; gateway for VLAN 10 hosts
- **VLAN 20 interface**: IP 192.168.20.1/24; gateway for VLAN 20 hosts
- Router accepts packets on VLAN 10, **routes to VLAN 20** based on destination IP

**Result**: Hosts in VLAN 10 → Route via router → Hosts in VLAN 20

## Methods to Create VLAN Interfaces on Router

### 1. [[Sub-interface]] (Traditional; Router on a Stick)
- **One physical port** on router; multiple **[[Sub-interface]]s** (tagged)
- Each [[Sub-interface]] = one [[VLAN]]
- Older/legacy approach; limited bandwidth (single port)

### 2. [[VLANIF Interface]] (Modern; Native to Huawei/Cisco)
- **Virtual layer 3 interface** per [[VLAN]]
- Not tied to physical port; more flexible
- **Preferred in modern networks**

Both achieve same goal: Inter-VLAN routing.

## Communication Flow

**Scenario**: PC1 (VLAN 10, 192.168.10.10) → PC2 (VLAN 20, 192.168.20.20)

1. **PC1 sends frame** (dest IP 192.168.20.20; dest MAC = router's VLAN 10 MAC)
2. **Switch receives** on [[Access Port]] (VLAN 10); floods or CAM-learns on [[Trunk Port]] (tagged)
3. **Router receives** on VLAN 10 interface; checks dest IP (192.168.20.20)
4. **Router routes** (IP routing table); forwards out VLAN 20 interface
5. **Router sends frame** (new frame; source MAC = router's VLAN 20 MAC)
6. **Switch receives** on [[Trunk Port]] tagged VLAN 20; floods or forwards to [[Access Port]]
7. **PC2 receives** (new frame; source IP = PC1; source MAC = router's VLAN 20 MAC)

**Key**: Both frames are **completely rewritten** by router (MAC address changed; layer 2 rebuilt).

## Layer 2 vs. Layer 3 Perspective

| Layer              | Same VLAN                    | Different VLAN                  |
| ------------------ | ---------------------------- | ------------------------------- |
| **L2 (Switching)** | Direct; MAC lookup on switch | NOT possible (not connected L2) |
| **L3 (Routing)**   | Not needed                   | Required; router decides path   |
|                    |                              |                                 |
|                    |                              |                                 |

[[VLAN]] isolates at **layer 2**. Inter-VLAN requires **layer 3 routing**.

## Inter-VLAN vs. Inter-Subnet

- **[[VLAN]]**: Layer 2 virtual network (broadcast domain)
- **Subnet**: Layer 3 logical network (IP address space)

Often **1:1 mapping**: VLAN 10 = Subnet 192.168.10.0/24; VLAN 20 = Subnet 192.168.20.0/24

But possible to have:
- Multiple subnets in one [[VLAN]] (no isolation at L3)
- One subnet spanning multiple [[VLAN]]s (layer 3 proxy; complex)

## Inter-VLAN Routing Methods

### Dynamic Routing (Large networks)
- [[OSPF]], [[RIP]], [[BGP]]
- Router learns routes from other routers
- Scales to multiple subnets/VLANs

### Static Routing
- Administrator manually defines routes
- Simple topology; few VLANs
- No overhead; requires manual updates

### Default Route
- All unknown traffic → single nexthop
- Works for simple hub-and-spoke VLANs

## Performance Consideration

**Traditional Problem**: Router processing every frame
- **Throughput**: Limited by router CPU
- **Latency**: Milliseconds per hop (L3 lookup)

**Modern Solution**:
- **Hardware routing**: Specialized [[ASIC]]s
- **Multi-layer switching**: Layer 3 switching (forwarding at L2 speed)
- **Route caching**: Most traffic flows cached; only first packet routed

## Related Concepts

- [[VLAN]]
- [[Sub-interface]]
- [[VLANIF Interface]]
- [[Routing]]
- [[Layer 3 Communication Process (Inter-VLAN)]]
- [[Access Port]]
- [[Trunk Port]]
- [[Native VLAN]]
