# Module 10: Inter-VLAN Communication

## 10.1 Inter-VLAN Communication

**Problem**: [[VLAN]]s are **layer 2 broadcast domains**; hosts in different [[VLAN]]s cannot communicate without **layer 3 routing**.

**Why isolation exists**:
- **[[VLAN]]** = segmented [[Broadcast Domain]]
- Layer 2 devices (switches) **cannot forward frames** across [[VLAN]] boundaries
- [[MAC Address Table (CAM)]] lookup fails for different [[VLAN]]s
- [[STP]] does not bridge between [[VLAN]]s

**Solution**: **[[Routing]]**
- Add **layer 3 gateway** for each [[VLAN]]
- Router/multi-layer switch routes packets between [[VLAN]]s
- Each [[VLAN]] gets unique IP subnet (e.g., VLAN 10 = 192.168.10.0/24; VLAN 20 = 192.168.20.0/24)

**Methods to implement**:
1. **[[Sub-interface]]**: Router with multiple tagged interfaces (Router on a Stick)
2. **[[VLANIF Interface]]**: Multi-layer switch with virtual layer 3 interfaces (modern)

**Key concept**: Hosts send traffic to **gateway IP** (default route); gateway forwards across [[VLAN]] boundaries at layer 3.

**Topology example**:
```
PC1 (VLAN 10)          PC2 (VLAN 20)
192.168.10.10/24       192.168.20.20/24
      │                     │
      └─ Switch ─────┬─────┘
                  (Isolated L2)
                     │
              ┌──────┴──────┐
          VLAN 10       VLAN 20
        Gateway 1.1     Gateway 20.1
              │              │
         Multi-layer Switch or Router
             (L3 Routing)
```

**Communication flow** (PC1 → PC2):
1. PC1 sends to gateway 192.168.10.1 (ARP resolves MAC)
2. Switch forwards frame to router/gateway (layer 2)
3. Router receives on VLAN 10 interface; checks routing table
4. Router routes to VLAN 20 interface (layer 3 decision)
5. Router resets frame: new source MAC (VLAN 20 gateway MAC), same IP
6. Switch forwards frame to VLAN 20 (layer 2)
7. PC2 receives (source MAC = gateway; source IP = PC1)

**Performance**:
- **Traditional router** ([[Sub-interface]]): CPU-based routing; bottleneck
- **Multi-layer switch** ([[VLANIF Interface]]): Hardware routing; fast

## 10.2 Sub-interfaces

**[[Sub-interface]]**: Layer 3 interface on router bound to single physical port using [[802.1Q]] tagging. Traditional "Router on a Stick" topology.

**Architecture**:
- **One physical port** (e.g., GigabitEthernet0/0/0)
- **Multiple [[Sub-interface]]s** (e.g., G0/0/0.10, G0/0/0.20)
- Each [[Sub-interface]] tags/untags one [[VLAN]] ID

**Switch-facing link**: [[Trunk Port]] carrying all [[VLAN]]s
```
[Switch] interface GigabitEthernet0/0/1
[Switch-GigabitEthernet0/0/1] port link-type trunk
[Switch-GigabitEthernet0/0/1] port trunk allow-pass vlan 10 20 30
```

**Router configuration**:
```
[Router] interface GigabitEthernet0/0/0
[Router-GigabitEthernet0/0/0] ip address 192.168.10.1 255.255.255.0
[Router-GigabitEthernet0/0/0] dot1q termination vid 10
[Router-GigabitEthernet0/0/0] arp broadcast enable
[Router-GigabitEthernet0/0/0] quit

[Router] interface GigabitEthernet0/0/0.20
[Router-GigabitEthernet0/0/0.20] ip address 192.168.20.1 255.255.255.0
[Router-GigabitEthernet0/0/0.20] dot1q termination vid 20
[Router-GigabitEthernet0/0/0.20] arp broadcast enable
[Router-GigabitEthernet0/0/0.20] quit
```

**Commands breakdown**:
- `dot1q termination vid 10`: Bind [[Sub-interface]] to [[VLAN]] 10 (802.1Q termination)
- `arp broadcast enable`: Allow ARP broadcasts within [[VLAN]]
- `ip address`: Gateway IP for [[VLAN]] 10
- Naming: `GigabitEthernet0/0/0.10` (port.VLAN ID)

**Bandwidth limitation**:
- Single physical port shared by all [[Sub-interface]]s
- If VLAN 10 and VLAN 20 both heavy traffic → **contention** on single 1 Gbps link
- Not scalable for many VLANs or high throughput

**When to use**:
- Small office (2-5 VLANs)
- Testing/lab
- Legacy routers without [[VLANIF Interface]] support
- Temporary workaround

## 10.3 VLANIF Interfaces

**[[VLANIF Interface]]**: Virtual layer 3 interface on **multi-layer switch** acting as gateway for [[VLAN]]. Modern replacement for [[Sub-interface]].

**Key difference**:
- [[Sub-interface]]: Tied to **physical port** (router with one port; Router on a Stick)
- [[VLANIF Interface]]: **Independent** of physical port (switch; all ports can use it)

**Architecture**:
- **Switch** has layer 2 (switching) + layer 3 (routing) combined
- **vlanif 10, vlanif 20, ..., vlanif N**: One per [[VLAN]]
- All switch ports can communicate through [[VLANIF Interface]]s (fast)

**Configuration**:
```
# Enable layer 3 routing
[Switch] ip routing

# Create VLANs
[Switch] vlan batch 10 20 30

# Create layer 3 gateways (vlanif)
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
```

**Advantages**:
✅ **No bandwidth constraint**: Not tied to single port (unlike [[Sub-interface]])
✅ **Fast routing**: Hardware-based ([[ASIC]]); microsecond latency
✅ **Scalable**: Supports 100-4000+ VLANs
✅ **Integrated**: Routing + switching in one device (no separate router)
✅ **Routing protocols**: Can run [[OSPF]], [[RIP]], [[BGP]] on [[VLANIF Interface]]s

**Disadvantages**:
❌ **Requires multi-layer switch**: Entry-level switches lack layer 3
❌ **Higher cost**: Multi-layer switch more expensive than L2 switch + separate router
❌ **Configuration complexity**: More commands than pure L2

**Comparison: [[VLANIF Interface]] vs. [[Sub-interface]]**

| Feature | [[VLANIF Interface]] | [[Sub-interface]] |
|---------|------|------|
| **Location** | Switch | Router |
| **Binding** | Virtual; independent | Physical port |
| **Bandwidth** | No constraint | Limited by port |
| **Speed** | Hardware (fast) | CPU (slow) |
| **Scalability** | Excellent (100+ VLANs) | Poor (5-10 max) |
| **Cost** | Higher (multi-layer switch) | Lower (router) |
| **Modern usage** | **Primary method** (90%+) | Legacy/small networks |

## 10.4 Layer 3 Communication Process

**Full step-by-step process** for two hosts in different [[VLAN]]s to communicate.

### Phase 1-2: Sender (PC1) Determines Gateway & Resolves Gateway MAC

**PC1**: 192.168.10.10/24 → PC2: 192.168.20.20/24

1. **PC1 calculates**: Destination 192.168.20.20 not in local subnet (192.168.10.0/24)
2. **Uses gateway**: Default route = 192.168.10.1 (router/[[VLANIF Interface]] IP for VLAN 10)
3. **ARP resolution**: "Who has 192.168.10.1?"
   - Broadcast in VLAN 10 (limited by [[Switch]] [[Broadcast Domain]])
   - Router/vlanif 10 responds: "I'm 192.168.10.1 at MAC AA:AA:AA:AA:AA:01"
4. **PC1 caches**: 192.168.10.1 = AA:AA:AA:AA:AA:01

### Phase 3-4: Sender Sends to Gateway; Switch Forwards

**PC1 transmits frame**:
```
Layer 2:  Source MAC: BB:BB:BB:BB:BB:11 (PC1)
          Dest MAC:   AA:AA:AA:AA:AA:01 (Gateway)
          VLAN:       10 (untagged on access port)

Layer 3:  Source IP:  192.168.10.10
          Dest IP:    192.168.20.20
```

**Switch actions**:
1. PC1 → Switch on [[Access Port]] (VLAN 10; untagged)
2. Switch MAC lookup: AA:AA:AA:AA:AA:01 in VLAN 10 (learned on [[Trunk Port]])
3. Switch → Router on [[Trunk Port]] (adds 802.1Q tag: VLAN 10)

### Phase 5-6: Router Routes; Resolves Destination MAC

**Router vlanif 10 receives**:
1. Strips [[VLAN]] 10 tag
2. Checks layer 3: Destination IP 192.168.20.20
3. **Routing lookup**: 192.168.20.0/24 → vlanif 20
4. **Egress**: vlanif 20; needs destination MAC

**Router ARP in VLAN 20**: "Who has 192.168.20.20?"
1. Router generates ARP request (broadcast on VLAN 20)
2. [[Switch]] floods VLAN 20 [[Broadcast Domain]] (to all ports in VLAN 20)
3. PC2 responds: "I'm 192.168.20.20 at MAC CC:CC:CC:CC:CC:22"
4. **Router caches**: 192.168.20.20 = CC:CC:CC:CC:CC:22

### Phase 7-8: Router Sends to Destination; Switch Forwards

**Router vlanif 20 transmits** (completely new frame):
```
Layer 2:  Source MAC: AA:AA:AA:AA:AA:02 (Router VLAN 20 MAC)
          Dest MAC:   CC:CC:CC:CC:CC:22 (PC2)
          VLAN:       20 (untagged on access port)

Layer 3:  Source IP:  192.168.10.10 (UNCHANGED)
          Dest IP:    192.168.20.20 (UNCHANGED)
          TTL:        63 (decremented from 64)
```

**Switch actions**:
1. Router → Switch on [[Trunk Port]] (adds VLAN 20 tag)
2. Switch MAC lookup: CC:CC:CC:CC:CC:22 in VLAN 20 (learned on [[Access Port]])
3. Switch → PC2 on [[Access Port]] (removes 802.1Q tag; untagged)

### Phase 9: Destination Receives

**PC2 receives** (complete new frame, same IP packet):
```
Layer 2:  Source MAC: AA:AA:AA:AA:AA:02 (Router)
          Dest MAC:   CC:CC:CC:CC:CC:22 (PC2)

Layer 3:  Source IP:  192.168.10.10 (PC1)
          Dest IP:    192.168.20.20 (PC2)
          TTL:        63
```

**Key observations**:
- **Source/Dest IP unchanged** (end-to-end)
- **Source/Dest MAC changed** (at each hop; layer 2 rebuilt)
- **TTL decremented** (anti-loop measure)
- **Frame rebuilt** completely by router (not relayed; routed)

### Frame & Packet Evolution Table

| Hop | Src MAC | Dst MAC | Src IP | Dst IP | VLAN | Notes |
|-----|---------|---------|--------|--------|------|-------|
| PC1 → Switch | BB:BB (PC1) | AA:AA (GW) | 10.10 | 20.20 | 10 (access) | Original frame |
| Switch → Router | BB:BB (PC1) | AA:AA (GW) | 10.10 | 20.20 | 10 (tagged) | Tagged on trunk |
| Router (routing) | *Rewrite* | *Rewrite* | 10.10 | 20.20 | *Route* | **L3 decision** |
| Router → Switch | AA:AA (GW2) | CC:CC (PC2) | 10.10 | 20.20 | 20 (tagged) | New frame |
| Switch → PC2 | AA:AA (GW2) | CC:CC (PC2) | 10.10 | 20.20 | 20 (access) | Untagged |

### Return Traffic (PC2 → PC1)

**Reverse process** (symmetric):
1. PC2 sends to default gateway 192.168.20.1 (vlanif 20 MAC AA:AA:AA:AA:AA:02)
2. Switch → Router on [[Trunk Port]] (tagged VLAN 20)
3. Router vlanif 20 receives; routes to PC1's subnet (192.168.10.0/24 via vlanif 10)
4. Router vlanif 10 egress; ARP resolves PC1 MAC (cached or ARP broadcast)
5. Router → Switch on [[Trunk Port]] (tagged VLAN 10)
6. Switch → PC1 on [[Access Port]] (untagged)

## Summary: Two Methods Comparison

| Aspect | [[Sub-interface]] | [[VLANIF Interface]] |
|--------|------|------|
| **Device** | Router | Multi-layer Switch |
| **Interface binding** | Physical port + [[VLAN]] tag | Virtual; independent |
| **Config example** | G0/0/0.10 (port.VLAN) | vlanif 10 |
| **Maximum VLANs** | ~10 (bandwidth limit) | 100-4000+ |
| **Throughput** | 100 Mbps - 10 Gbps | 100 Gbps+ |
| **Latency** | 100+ microseconds | <1 microsecond |
| **Cost** | Lower | Higher |
| **Scalability** | Poor | Excellent |
| **Modern networks** | Legacy (20%+) | Primary (80%+) |
| **Use case** | Small office; testing | Enterprise; production |

## Best Practices

### VLAN Design
- **1 VLAN = 1 subnet**: VLAN 10 = 192.168.10.0/24 (gateway 192.168.10.1)
- **Consistent naming**: VLAN ID matches subnet third octet
- **Avoid VLAN 1**: Reserved; isolate management (VLAN 999)

### Gateway Assignment
- **Primary gateway**: Router/vlanif (always active)
- **Secondary gateway** (redundancy): [[HSRP]]/[[VRRP]] (virtual IP; shared by two routers)
- **Default route**: All hosts point to vlanif IP

### Monitoring
```
# View vlanif status
display interface vlanif

# Check routing table
display ip routing-table

# Verify inter-VLAN connectivity
ping 192.168.20.20 (from VLAN 10 host)
```

### Troubleshooting
| Issue | Cause | Fix |
|-------|-------|-----|
| PC cannot reach other VLAN | No [[VLANIF Interface]]/[[Sub-interface]] | Create gateway on both VLANs |
| ARP timeout | Host doesn't respond; MAC unknown | Verify host online; check [[Access Port]] config |
| Intermittent connectivity | [[STP]]/[[RSTP]] reconvergence | Check [[Topology Change]]s; verify [[Root Bridge]] |
| High latency | Router CPU bottleneck ([[Sub-interface]]) | Migrate to [[VLANIF Interface]]/multi-layer switch |

