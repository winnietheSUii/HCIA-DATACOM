# Open Shortest Path First (OSPF)

A classless [[Link-State Routing Protocols|link-state]] [[Interior Gateway Protocols (IGPs)|interior gateway protocol]] standardized in RFC 2328 (OSPFv2) and RFC 5340 (OSPFv3).

## Core Characteristics

- **Link-State Algorithm**: Maintains complete [[Network-Topology|network topology]] via [[Link-State Database (LSDB)]]
- **SPF Calculation**: Uses Dijkstra's shortest path first algorithm
- **Metric**: Cost = Reference Bandwidth (default 100 Mbps) / Interface Bandwidth
- **Convergence**: Fast convergence to topology changes
- **Classless**: Supports CIDR and variable-length subnet masks (VLSM)
- **Intra-AS Routing**: Routes within a single [[Autonomous System (AS)]]

## Key Components

### OSPF Areas
- Hierarchical network design using areas
- **Area 0 (Backbone)**: Core area; all other areas must connect to it
- **Regular Areas**: Numbered 1, 2, 3, etc.
- Reduces LSDB size and improves scalability

### Router Types

- **Internal Router**: All interfaces in same area
- **[[Area Border Router (ABR)]]**: Connects multiple areas; summarizes routes
- **[[Autonomous System Boundary Router (ASBR)]]**: Injects external routes (e.g., from BGP)
- **Backbone Router**: Has at least one interface in Area 0

### Network Types

- **[[Broadcast Multiple Access (BMA)]]** (Ethernet): Elects [[Designated Router (DR)]] and [[Backup Designated Router (BDR)]]
- **Point-to-Point**: Direct connection; no DR/BDR
- **Point-to-Multipoint**: No DR/BDR; hybrid approach
- **Non-Broadcast Multiple Access (NBMA)**: Manual neighbor configuration

### Packet Types

1. **Hello**: Establishes and maintains neighbor relationships
2. **[[Database Description (DD)]]**: Exchanges LSDB summaries
3. **Link State Request**: Requests missing LSAs
4. **Link State Update**: Floods new or updated LSAs
5. **Link State Acknowledgment**: Confirms LSA reception

## Adjacency States

1. **Down**: No Hello received
2. **Init**: Hello received but not bidirectional
3. **2-Way**: Bidirectional communication established
4. **ExStart**: Begin database exchange
5. **Exchange**: Exchanging [[Database Description (DD)]] packets
6. **Loading**: Requesting missing LSAs
7. **Full**: Complete adjacency; databases synchronized

## Advantages

- Fast convergence after topology changes
- Efficient bandwidth usage (after initial sync)
- Supports hierarchical design with areas
- Loop prevention via topology awareness
- Load balancing across equal-cost paths

## Disadvantages

- Higher CPU and memory requirements
- More complex configuration than RIP
- Requires careful area design in large networks
- All routers in area must have identical LSDB

## Configuration Example

```
ospf 1
  area 0
    network 192.168.1.0 0.0.0.255
    network 192.168.2.0 0.0.0.255
```

## Related Protocols

- **RIP**: Distance-vector alternative (simpler but slower)
- **IS-IS**: Similar link-state protocol used in ISP networks
- **EIGRP**: Cisco proprietary hybrid protocol
