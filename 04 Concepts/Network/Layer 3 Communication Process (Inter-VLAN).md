# Layer 3 Communication Process (Inter-VLAN)

Step-by-step process for two hosts in different [[VLAN]]s to communicate. Requires **layer 3 routing** via [[Router]] or multi-layer switch [[VLANIF Interface]].

## Prerequisites

1. **Both hosts reachable** at layer 2 (within same switch domain or interconnected via [[Trunk Port]])
2. **Router/switch has layer 3 gateways** for both VLANs ([[Sub-interface]] or [[VLANIF Interface]])
3. **Routing table** populated (static or dynamic route between subnets)
4. **[[STP]]/RSTP active** (if redundant paths exist)

## Step-by-Step Process

### Phase 1: PC1 (VLAN 10) Determines Gateway

**PC1**: 192.168.10.10/24; needs to reach PC2: 192.168.20.20

1. **PC1 calculates**: Destination 192.168.20.20 not in local subnet (192.168.10.0/24)
2. **PC1 behavior**: Must use gateway (default route) to reach different subnet
3. **Gateway IP**: **192.168.10.1** (configured on [[VLANIF Interface]]/[[Sub-interface]] for VLAN 10)
4. **PC1 action**: Send packet to gateway MAC address

### Phase 2: ARP Resolution (PC1 → Gateway)

**PC1 needs gateway MAC address** (has IP 192.168.10.1):

1. **PC1 sends ARP request** (broadcast):
   - "Who has 192.168.10.1? Tell 192.168.10.10"
   - Dest MAC: FF:FF:FF:FF:FF:FF (broadcast)
   - Dest IP: 192.168.10.1

2. **Switch layer 2 action** (flooding):
   - ARP request floods in VLAN 10 ([[Broadcast Domain]])
   - [[Access Port]]s in VLAN 10 receive broadcast
   - [[Trunk Port]]s do NOT flood (broadcast limited to VLAN 10)

3. **Router/[[VLANIF Interface]] responds**:
   - vlanif 10 (IP 192.168.10.1) sends **ARP reply**
   - "192.168.10.1 is at MAC AA:AA:AA:AA:AA:01" (router's VLAN 10 MAC)

4. **PC1 receives ARP reply**:
   - Caches: 192.168.10.1 = AA:AA:AA:AA:AA:01
   - Now knows gateway MAC

### Phase 3: PC1 Sends Frame to Gateway

**PC1 encapsulates packet**:

```
Frame (Layer 2):
  Source MAC:      (PC1 MAC, e.g., BB:BB:BB:BB:BB:11)
  Dest MAC:        AA:AA:AA:AA:AA:01 (Gateway)
  VLAN ID:         10 (if on trunk; usually untagged on access port)
  EtherType:       0x0800 (IPv4)
  
Packet (Layer 3):
  Source IP:       192.168.10.10
  Dest IP:         192.168.20.20
  TTL:             64
  
Data (Layer 4+):   TCP/UDP payload
```

**PC1 → Switch**:
1. PC1 transmits on [[Access Port]] (VLAN 10)
2. [[Access Port]] does NOT add tag (assumed VLAN 10)
3. Switch floods based on **dest MAC lookup**:
   - Destination MAC AA:AA:AA:AA:AA:01 (gateway)
   - Switch learned this MAC on [[Trunk Port]] (router port) for VLAN 10

### Phase 4: Switch Forwards to Router/Gateway

**Switch layer 2 action**:

1. **MAC lookup**: Destination AA:AA:AA:AA:AA:01 in VLAN 10
2. **Outgoing port**: [[Trunk Port]] toward router (port learned MAC on)
3. **Frame forwarding**:
   - **Trunk port rule**: Add 802.1Q tag for VLAN 10
   - Frame sent to router with **tag: VLAN 10**

4. **Router receives** (on [[Sub-interface]] G0/0/0.10 or vlanif 10):
   - Strips VLAN 10 tag
   - Extracts [[IPv4]] packet
   - Checks dest IP: 192.168.20.20

### Phase 5: Router Layer 3 Routing Decision

**Router/switch performs routing**:

1. **Routing table lookup**: Destination 192.168.20.20
   - Matches route: 192.168.20.0/24 via vlanif 20 (or G0/0/0.20)
   - Next hop: vlanif 20 (local gateway)

2. **TTL decrement**: TTL 64 → 63 (hop count)

3. **Layer 2 rewrite**:
   - **New source MAC**: vlanif 20 MAC (AA:AA:AA:AA:AA:02)
   - **New dest MAC**: PC2 MAC (needs ARP to find)

### Phase 6: Router Resolves PC2 MAC (ARP in VLAN 20)

**Router vlanif 20 needs PC2 MAC** (knows IP 192.168.20.20):

1. **Router sends ARP request** (on VLAN 20):
   - "Who has 192.168.20.20? Tell 192.168.20.1"
   - Dest MAC: FF:FF:FF:FF:FF:FF (broadcast)

2. **Switch layer 2 action**:
   - ARP request generated on vlanif 20 (VLAN 20 broadcast)
   - Floods from [[Trunk Port]] to [[Access Port]]s in VLAN 20
   - [[Access Port]] in VLAN 20 receives untagged broadcast

3. **PC2 receives ARP request**:
   - "Router is at 192.168.20.1? Tell 192.168.20.20"
   - PC2 responds: "I'm 192.168.20.20; my MAC is CC:CC:CC:CC:CC:22"

4. **Router caches ARP**:
   - 192.168.20.20 = CC:CC:CC:CC:CC:22 (PC2 MAC)

### Phase 7: Router Sends Frame to PC2

**Router vlanif 20 transmits**:

```
Frame (Layer 2):
  Source MAC:      AA:AA:AA:AA:AA:02 (vlanif 20 MAC)
  Dest MAC:        CC:CC:CC:CC:CC:22 (PC2 MAC)
  VLAN ID:         20
  EtherType:       0x0800 (IPv4)
  
Packet (Layer 3):
  Source IP:       192.168.10.10 (UNCHANGED)
  Dest IP:         192.168.20.20 (UNCHANGED)
  TTL:             63 (decremented by router)
```

**Key**: Layer 3 packet unchanged (source/dest IP same); only layer 2 MAC addresses changed.

**Router → Switch**:
1. Router egress on vlanif 20 (VLAN 20)
2. Sends frame toward VLAN 20
3. Switch [[Trunk Port]] adds VLAN 20 tag

### Phase 8: Switch Forwards to PC2

**Switch layer 2 action**:

1. **MAC lookup**: Destination CC:CC:CC:CC:CC:22 in VLAN 20
2. **Outgoing port**: [[Access Port]] where PC2 is learned
3. **Frame forwarding**:
   - **Access port rule**: **Remove** 802.1Q tag
   - Frame sent to PC2 **untagged**

4. **PC2 receives** (untagged frame):
   - Layer 2: Source MAC AA:AA:AA:AA:AA:02 (router's VLAN 20 MAC)
   - Layer 3: Source IP 192.168.10.10 (PC1); Dest IP 192.168.20.20 (PC2)

### Phase 9: PC2 Processes Packet

**PC2 receives**:

1. **Layer 2 check**: Frame for PC2 MAC; OK
2. **Layer 3 check**: Packet for PC2 IP; OK
3. **Data extraction**: Payload from layer 4
4. **Response** (if needed): PC2 → PC1 (reverse path)

## Summary: Frame & Packet Evolution

| Hop | Source MAC | Dest MAC | Source IP | Dest IP | VLAN | Notes |
|-----|-----------|----------|-----------|---------|------|-------|
| **PC1 → Switch** | PC1 (BB:BB:BB:BB:BB:11) | Router (AA:AA:AA:AA:AA:01) | 192.168.10.10 | 192.168.20.20 | 10 (untagged on access) | PC1 sends to gateway |
| **Switch → Router** | PC1 (BB:BB:BB:BB:BB:11) | Router (AA:AA:AA:AA:AA:01) | 192.168.10.10 | 192.168.20.20 | 10 (tagged on trunk) | Switch tags for trunk |
| **Router (internal)** | *Frame rewritten* | *New destination* | 192.168.10.10 | 192.168.20.20 | *Routed* | **L3 routing decision** |
| **Router → Switch** | Router (AA:AA:AA:AA:AA:02) | PC2 (CC:CC:CC:CC:CC:22) | 192.168.10.10 | 192.168.20.20 | 20 (tagged on trunk) | Router egress VLAN 20 |
| **Switch → PC2** | Router (AA:AA:AA:AA:AA:02) | PC2 (CC:CC:CC:CC:CC:22) | 192.168.10.10 | 192.168.20.20 | 20 (untagged on access) | Switch untags for access |

## Key Points

1. **[[VLAN]] = Layer 2 boundary**: Crossing [[VLAN]] requires layer 3 routing
2. **MAC addresses rewritten**: Each hop changes source/dest MAC
3. **IP addresses unchanged**: Source/dest IP preserved end-to-end
4. **ARP per VLAN**: Router must resolve MACs within each [[VLAN]]'s broadcast domain
5. **Tagging**: [[Trunk Port]]s add 802.1Q tag; [[Access Port]]s remove it
6. **TTL decremented**: Router decrements TTL (hop count) to prevent loops

## Failure Scenarios

| Failure | Symptom | Cause |
|---------|---------|-------|
| **Gateway unreachable** | PC1 cannot reach any PC2 | No [[VLANIF Interface]]/[[Sub-interface]] for VLAN 10 |
| **Route missing** | PC1 reaches gateway; gateway cannot find PC2 | No route 192.168.20.0/24 in routing table |
| **PC2 MAC unresolved** | Gateway times out | ARP blocked; PC2 not responding; [[Access Port]] misconfigured |
| **VLAN mismatch** | PC2 not in VLAN 20 | [[Access Port]] assigned wrong VLAN |
| **Link down** | Intermittent connectivity | Physical port failure; [[STP]] blocking [[Trunk Port]] |

## Related Concepts

- [[Inter-VLAN Communication]]
- [[VLANIF Interface]]
- [[Sub-interface]]
- [[VLAN]]
- [[Access Port]]
- [[Trunk Port]]
- [[ARP Basics]]
- [[Routing]]
- [[MAC Address]]
- [[802.1Q]]
