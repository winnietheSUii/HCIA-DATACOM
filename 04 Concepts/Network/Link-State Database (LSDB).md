# Link-State Database (LSDB)

A complete record of all Link State Advertisements (LSAs) maintained by each router in a [[Link-State Routing Protocols|link-state routing protocol]] domain. The LSDB represents the complete network topology known to that router.

## Purpose

- **Topology Knowledge**: Router knows the complete network topology
- **Route Calculation**: Input for SPF algorithm to compute shortest paths
- **Consistency**: All routers in area/domain maintain identical LSDB
- **Synchronization**: Ensures all routers agree on network state

## LSDB Contents

Each LSDB entry (LSA) contains:

- **LS Type**: Type of link state (router, network, external, etc.)
- **Link State ID**: Identifies the LSA originator or network
- **Advertising Router**: Router that created the LSA
- **LS Sequence Number**: Tracks LSA versions; higher = newer
- **LS Age**: Time in seconds since LSA was originated (max 3600)
- **Link Information**: Neighbors, costs, and network details

## In [[Open Shortest Path First (OSPF)]]

### LSDB Types by Area

| Area Type         | LSDB Contents                                               |
| ----------------- | ----------------------------------------------------------- |
| Backbone (Area 0) | Router LSAs + Network LSAs for all areas + summary LSAs     |
| Regular Area      | Router LSAs + Network LSAs for area + summary LSAs from ABR |
| Stub Area         | Router LSAs + Network LSAs + summary LSA for default route  |

### OSPF LSA Types

1. **Type 1 (Router LSA)**: Neighbors and links for router originating it
2. **Type 2 (Network LSA)**: Describes network and routers on broadcast segment (sent by DR)
3. **Type 3 (Summary LSA)**: Routes between areas (sent by ABR)
4. **Type 4 (Summary LSA)**: Route to [[Autonomous System Boundary Router (ASBR)]]
5. **Type 5 (External LSA)**: Routes external to [[Autonomous System (AS)]]
6. **Type 7 (NSSA LSA)**: External routes in NSSA areas (converted to Type 5 by ABR)

## Synchronization Process

```
Step 1: Hello Exchange
  R1 ←→ R2 (establish communication)

Step 2: Database Description Exchange
  R1 sends DD packets listing LSA headers it has
  R2 compares to local LSDB

Step 3: Link State Request
  R2 requests any LSAs missing from its LSDB

Step 4: Link State Update
  R1 sends full LSAs to R2

Step 5: Full Adjacency
  Both routers have identical LSDB
  State = Full (ready to route)
```

## LSA Flooding

- **Triggered**: New LSA or change in existing LSA
- **Reliable**: All routers acknowledge LSA reception
- **Scoped**: Flooding limited to area (Type 1, 2, 3) or entire AS (Type 5)
- **Periodic**: LSAs refreshed every 30 minutes (sequence number increments)

## LSDB Size Impact

- **Larger areas** = larger LSDB = more memory/CPU
- **Multiple areas** = multiple smaller LSDBs = better scalability
- **Route summarization** = fewer LSAs = smaller LSDB

## Example LSDB Sizes

```
Single Area (Area 0): 500 networks
  LSDB Size: ~2 MB (500 Router LSAs + 500 Network LSAs + links)

Multi-Area (Area 0 + 3 areas): Same 500 networks
  Area 0 LSDB: ~2.5 MB (includes summaries)
  Area 1 LSDB: ~200 KB (area-specific + summaries)
  Area 2 LSDB: ~200 KB
  Area 3 LSDB: ~200 KB
  Total memory: ~3.1 MB (more efficient distribution)
```

## Verification Commands (Huawei VRP)

```
display ospf lsdb              ; Show full LSDB
display ospf lsdb router       ; Show only Router LSAs
display ospf lsdb network      ; Show only Network LSAs
display ospf lsdb summary      ; Show only Summary LSAs
```

## Related Concepts

- [[Open Shortest Path First (OSPF)]]
- [[Link-State Routing Protocols]]
- [[Area Border Router (ABR)]]
- [[Designated Router (DR)]]
- SPF algorithm (Dijkstra's shortest path first)
