# Area Border Router (ABR)

A router in multi-area [[Open Shortest Path First (OSPF)]] networks that has interfaces in multiple [[Autonomous System (AS)|OSPF areas]], summarizes routes between areas, and connects non-backbone areas to the backbone (Area 0).

## Purpose

- **Area Connectivity**: Links multiple areas to backbone
- **Route Summarization**: Reduces LSDB size by aggregating routes
- **Traffic Engineering**: Controls inter-area traffic flow
- **Scalability**: Enables hierarchical network design

## Placement in Network

```
OSPF Multi-Area Network:

Area 0 (Backbone)
├─ ABR-A [Router ID 1.1.1.1] ───── Area 1
└─ ABR-B [Router ID 2.2.2.2] ───── Area 2
   [All have interfaces in Area 0]
```

## Key Responsibilities

### 1. Multiple Area Membership
- At least one interface in backbone (Area 0)
- One or more interfaces in non-backbone areas
- Each interface runs independent OSPF adjacency process

### 2. Route Summarization
- Collects all routes in attached area
- Creates summary route for area (Type 3 LSA)
- Advertises summary to other areas
- **Reduces routing table size** and LSDB propagation

### 3. LSDB Management
- Maintains separate LSDB for each connected area
- Area 0 LSDB = largest (contains all backbone routes + area summaries)
- Area LSDB = only area-specific routes + summaries from other areas via ABR

### 4. Inter-Area Routing
- Advertises Area 0 routes to non-backbone areas
- Advertises area-specific routes to other areas (via Area 0)
- Non-backbone areas must communicate through Area 0 (via ABR)

## Example Configuration

```
! ABR connecting Area 0 and Area 1

ospf 1
  area 0
    network 10.0.0.0 0.0.0.255  ; Interface in backbone
  area 1
    network 192.168.1.0 0.0.0.255  ; Interface in Area 1
```

## Summarization Example

```
Area 1 contains:
- 192.168.1.0/24
- 192.168.2.0/24
- 192.168.3.0/24
- 192.168.4.0/24

ABR creates summary:
- 192.168.0.0/22 (covers all 4 subnets)
- Advertises to other areas

Result:
  Other areas see 1 route instead of 4 = smaller routing table
```

## ABR vs. ASBR

| Feature | ABR | ASBR |
|---------|-----|------|
| Purpose | Connect areas within AS | Connect ASes |
| Interfaces | In multiple OSPF areas | In OSPF area + external network |
| Routes | Summarizes inter-area routes | Injects external AS routes |
| Example | Multi-area OSPF | OSPF + BGP router |

## Importance

- **Mandatory for multi-area OSPF**: Every area except Area 0 must have ABR connection
- **Improves scalability**: Route summarization prevents LSDB explosion
- **Enables hierarchy**: Transforms flat topology into structured design

## Related Concepts

- [[Open Shortest Path First (OSPF)]]
- [[Autonomous System (AS)]]
- [[Autonomous System Boundary Router (ASBR)]]
- Route summarization
- OSPF areas (Area 0 = backbone)
