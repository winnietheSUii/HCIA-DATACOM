# Module 6: OSPF Basics

## 6.1 OSPF Overview

### 6.1.1 Classification of Dynamic Routing Protocols

OSPF is a **Link-State Routing Protocol** and an **Interior Gateway Protocol (IGP)**.

- **Link-State**: Routers maintain a complete topology database (LSDB) and flood topology information
- **IGP**: Used within an [[Autonomous System (AS)]] for intra-AS routing
- Alternatives: RIP (distance-vector), EIGRP (hybrid), IS-IS (link-state)

### 6.1.2 Introduction to OSPF

[[Open Shortest Path First (OSPF)]] is a classless, convergent routing protocol:

- **Link-State Database (LSDB)**: Maintains complete network topology
- Metric: **Cost** = Reference Bandwidth (default 100Mbps) / Interface Bandwidth
- **Hierarchical design**: Organized using **Areas** (Area 0 is backbone)
- **Convergence**: Fast convergence using SPF algorithm
- **OSPF packet types**: Hello, Database Description ([[Database Description (DD)]]), Link State Request, Link State Update, Link State Acknowledgment

### 6.1.3 OSPF Process

An [[OSPF Process]] is an instance of the OSPF routing protocol running on a router:

- **Process ID**: Unique identifier for each OSPF instance (locally significant only)
- **Multiple processes**: A router can run multiple OSPF processes simultaneously
- **Independent operation**: Each process maintains its own:
  - Link-State Database (LSDB)
  - Routing table
  - SPF tree calculations
  - Area configuration
- **Process ID scope**: Does NOT need to match between routers to form neighbors
- **Use cases**: Routing domain separation, migration scenarios, multi-tenancy

**Example**: `ospf 1` creates OSPF process with ID 1

## 6.2 OSPF Working Mechanism

### 6.2.1 Establishing an OSPF Adjacency Relationship

OSPF neighbors progress through multiple states before forming an adjacency:

1. **Down**: No Hello packets received
2. **Init**: Received Hello from neighbor (but not yet bidirectional)
3. **2-Way**: Neighbor lists local router in its Hello packet (bidirectional communication established)
4. **ExStart**: Begin exchanging database description packets
5. **Exchange**: Sending Database Description ([[Database Description (DD)]]) packets
6. **Loading**: Requesting missing LSAs via Link State Request
7. **Full**: Complete OSPF adjacency; routers synchronized

**Key process**: Hello packets → Database sync → Route calculation using SPF algorithm

### 6.2.2 OSPF Network Types

Different network types affect [[Designated Router (DR)]] election and flooding behavior:

1. **Broadcast Multiple Access (BMA)** - Ethernet LAN
   - Elects [[Designated Router (DR)]] and [[Backup Designated Router (BDR)]]
   - Reduces adjacencies and LSA flooding
   - All routers establish adjacency with DR/BDR only

2. **Point-to-Point** - Serial links, GRE tunnels
   - No DR/BDR election needed
   - Direct adjacency between two routers

3. **Point-to-Multipoint** - Hybrid approach
   - No DR/BDR; treats as multiple point-to-point links
   - Used for partial mesh networks

4. **Non-Broadcast Multiple Access (NBMA)** - Frame Relay, ATM
   - Requires manual neighbor configuration
   - DR/BDR election occurs

**[[Autonomous System (AS)]] considerations**: 
- OSPF operates within a single AS
- External routes learned via [[Area Border Router (ABR)]] and [[Autonomous System Boundary Router (ASBR)]]

## 6.3 Typical OSPF Configuration

### 6.3.1 Basic OSPF Configuration

Essential steps to enable OSPF on a Huawei VRP device:

1. **Enable OSPF process**:
   - Create an OSPF process ID (can run multiple instances in enterprise networks)
   - Example: `ospf 1`

2. **Define Areas**:
   - Hierarchical organization; Area 0 is backbone (mandatory)
   - Other areas connect through ABRs to Area 0
   - Example: `area 0`, `area 1`

3. **Advertise Networks**:
   - Enable OSPF on specific interfaces or networks
   - Match interfaces to areas
   - Example: `network 192.168.1.0 0.0.0.255 area 0`

4. **Verify Configuration**:
   - Check neighbor relationships: `display ospf peer`
   - View routing table: `display ip routing-table`
   - Examine LSDB: `display ospf lsdb`

### 6.3.2 OSPF Configuration Example

**Scenario**: Three routers (R1, R2, R3) in a simple topology

```
R1 -- R2 -- R3
(Area 0 backbone)
```

**R1 Configuration** (Huawei VRP):
```
ospf 1
 area 0
  network 192.168.1.0 0.0.0.255
  network 192.168.12.0 0.0.0.255
```

**R2 Configuration**:
```
ospf 1
 area 0
  network 192.168.12.0 0.0.0.255
  network 192.168.23.0 0.0.0.255
```

**R3 Configuration**:
```
ospf 1
 area 0
  network 192.168.23.0 0.0.0.255
  network 192.168.3.0 0.0.0.255
```

**Result**: All routers learn routes to all networks; OSPF cost calculated per interface
