# L6: OSPF Basics

## Module 1: Understanding OSPF

Domain D3.3.1, D3.3.2, D3.3.3

### Introduction to OSPF

**OSPF (Open Shortest Path First)** is a link-state interior gateway protocol (IGP) that uses the Dijkstra algorithm to calculate the shortest path to each destination.

### OSPF Characteristics

- **Link-State Protocol:** Routers share complete topology information
- **Fast Convergence:** Quickly adapts to network changes
- **Hierarchical Design:** Uses areas for scalability
- **Classless:** Supports VLSM and CIDR
- **Open Standard:** Defined in RFC 2328 (OSPFv2)
- **Uses IP Protocol 89**
- **Multicast Addresses:** 224.0.0.5 (All OSPF routers), 224.0.0.6 (DR/BDR)

### OSPF Terminology

| Term | Description |
|------|-------------|
| **Router ID** | Unique 32-bit identifier for each OSPF router |
| **Area** | Logical grouping of routers for scalability |
| **Backbone Area** | Area 0, central area connecting all other areas |
| **ABR** | Area Border Router, connects areas |
| **ASBR** | Autonomous System Boundary Router, connects to external networks |
| **LSA** | Link-State Advertisement, routing information unit |
| **LSDB** | Link-State Database, collection of all LSAs |
| **DR** | Designated Router, reduces adjacencies on multi-access networks |
| **BDR** | Backup Designated Router |
| **SPF** | Shortest Path First algorithm |

### OSPF Router Types

1. **Internal Router:** All interfaces in same area
2. **Backbone Router:** At least one interface in Area 0
3. **ABR (Area Border Router):** Connects multiple areas
4. **ASBR (AS Boundary Router):** Connects to external routing domains

### OSPF Areas

**Area 0 (Backbone):**
- Central area
- All other areas must connect to Area 0
- Required for inter-area routing

**Regular Areas:**
- Connected to backbone via ABR
- Contain internal routers

**Special Area Types:**
- **Stub Area:** Blocks external routes
- **Totally Stubby Area:** Blocks external and inter-area routes
- **NSSA:** Not-So-Stubby Area, allows limited external routes

### OSPF Packet Types

| Type | Name | Function |
|------|------|----------|
| 1 | Hello | Neighbor discovery and maintenance |
| 2 | DBD (Database Description) | LSDB summary exchange |
| 3 | LSR (Link-State Request) | Request specific LSAs |
| 4 | LSU (Link-State Update) | Send requested LSAs |
| 5 | LSAck (Link-State Acknowledgment) | Confirm LSA receipt |

### OSPF Neighbor States

1. **Down:** Initial state, no Hello received
2. **Attempt:** (NBMA only) Trying to contact neighbor
3. **Init:** Hello received, but own Router ID not seen
4. **2-Way:** Bidirectional communication established
5. **ExStart:** Master/slave negotiation for DBD exchange
6. **Exchange:** DBD packets exchanged
7. **Loading:** LSAs being requested and received
8. **Full:** LSDB synchronized, adjacency formed

### OSPF Network Types

| Type | Hello/Dead Timer | DR/BDR | Example |
|------|------------------|--------|---------|
| Broadcast | 10/40 sec | Yes | Ethernet |
| Point-to-Point | 10/40 sec | No | Serial |
| NBMA | 30/120 sec | Yes | Frame Relay |
| Point-to-Multipoint | 30/120 sec | No | Various |

### DR/BDR Election

On multi-access networks (Broadcast, NBMA):
- DR reduces LSA flooding overhead
- BDR provides backup if DR fails
- Election based on:
  1. Highest OSPF priority (default 1, 0 = ineligible)
  2. Highest Router ID (if priority tied)

```
[Huawei-GigabitEthernet0/0/1] ospf dr-priority 100
```

### OSPF Cost Calculation

OSPF uses cost as its metric, based on interface bandwidth.

**Formula:** Cost = Reference Bandwidth / Interface Bandwidth

**Default Reference Bandwidth:** 100 Mbps

| Interface | Bandwidth | Default Cost |
|-----------|-----------|--------------|
| 10 Mbps | 10 Mbps | 10 |
| FastEthernet | 100 Mbps | 1 |
| GigabitEthernet | 1000 Mbps | 1 |
| 10 GigabitEthernet | 10000 Mbps | 1 |

**Adjust Reference Bandwidth:**
```
[Huawei-ospf-1] bandwidth-reference 10000
```

### OSPF Configuration

#### Enable OSPF Process
```
[Huawei] ospf 1 router-id 1.1.1.1
[Huawei-ospf-1]
```

#### Configure Areas and Networks
```
[Huawei-ospf-1] area 0
[Huawei-ospf-1-area-0.0.0.0] network 192.168.1.0 0.0.0.255
[Huawei-ospf-1-area-0.0.0.0] network 10.0.0.0 0.0.0.255
```

#### Interface-based Configuration
```
[Huawei] interface GigabitEthernet 0/0/1
[Huawei-GigabitEthernet0/0/1] ospf enable 1 area 0
```

#### Set Interface Cost
```
[Huawei-GigabitEthernet0/0/1] ospf cost 100
```

### OSPF Verification Commands

```
<Huawei> display ospf brief
<Huawei> display ospf peer
<Huawei> display ospf routing
<Huawei> display ospf lsdb
<Huawei> display ospf interface
```

### OSPF LSA Types

| Type | Name | Description | Flooded |
|------|------|-------------|---------|
| 1 | Router LSA | Router links info | Within area |
| 2 | Network LSA | DR creates for multi-access | Within area |
| 3 | Summary LSA | ABR summarizes routes | Between areas |
| 4 | ASBR Summary | Location of ASBR | Between areas |
| 5 | External LSA | External routes | Entire AS |
| 7 | NSSA External | External routes in NSSA | Within NSSA |

### OSPF Authentication

#### Area Authentication
```
[Huawei-ospf-1-area-0.0.0.0] authentication-mode md5 1 cipher Huawei@123
```

#### Interface Authentication
```
[Huawei-GigabitEthernet0/0/1] ospf authentication-mode md5 1 cipher Huawei@123
```

### OSPF Troubleshooting

Common issues:
1. **Neighbor not forming:** Check Hello/Dead timers, area ID, authentication
2. **Routes not appearing:** Check network statements, area configuration
3. **Suboptimal routing:** Check costs, verify LSDB consistency

```
<Huawei> display ospf error
<Huawei> display ospf peer verbose
<Huawei> display ospf lsdb router
```

### Summary

OSPF is a robust, scalable routing protocol:
- Link-state protocol with fast convergence
- Uses areas for hierarchical design
- DR/BDR reduces overhead on multi-access networks
- Cost-based metric derived from bandwidth
- Multiple LSA types for different information
- Supports authentication for security
