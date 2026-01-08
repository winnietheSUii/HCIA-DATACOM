# Designated Router (DR)

A router elected on [[Broadcast Multiple Access (BMA)]] networks (like Ethernet LANs) to reduce [[Open Shortest Path First (OSPF)]] flooding overhead and number of adjacencies.

## Why DR/BDR Election?

### Without DR/BDR (Point-to-Point) (mesh)
- $n$ routers = $n(n-1)/2$ adjacencies and LSA floods
- Example: 4 routers = 6 adjacencies = high bandwidth use

### With DR/BDR (Broadcast)
- All routers form adjacency **only** with DR and [[Backup Designated Router (BDR)]]
- Only DR floods LSAs to all routers
- Dramatically reduces bandwidth and CPU load
- Example: 4 routers = 3 adjacencies (each to DR)

## Election Criteria

1. **Router Priority** (0-255, default 10)
   - Higher priority wins
   - Priority 0 = never elected DR
   
2. **Router ID** (tiebreaker)
   - Highest Router ID wins if priorities tie
   - Router ID = highest loopback IP or interface IP

3. **Non-preemptive**
   - Once elected, DR keeps role even if higher-priority router joins
   - Exception: DR goes down, then new election occurs

## Role in OSPF

- **DR Responsibilities**
  - Forms adjacencies with all routers on subnet (DR-other routers become Full)
  - Floods LSAs from other routers (multicasts to **all routers**: 224.0.0.5)
  - Originates network LSAs for the subnet

- **BDR Role**
  - Backup to DR; also forms full adjacency with all routers
  - Listens to all exchanges but doesn't originate LSAs
  - Becomes DR if current DR fails

- **DR-Other Routers**
  - Only form adjacency with DR and BDR (state = 2-Way with others)
  - Send LSAs to DR (unicast to 224.0.0.6 = **all designated routers**)
  - Do not establish full adjacency with each other

## Configuration Example

```
interface Ethernet0/0
  ip ospf priority 100  ; Higher priority = more likely to be DR
```

## Impact on Network Design

- Reduces load on broadcast networks (Ethernet switches, hubs)
- Critical for hub-and-spoke topologies (DR = hub)
- Non-preemptive design simplifies failover
- BDR election ensures fast failover if DR dies

## Related Concepts

- [[Backup Designated Router (BDR)]]
- [[Broadcast Multiple Access (BMA)]]
- [[Open Shortest Path First (OSPF)]]
