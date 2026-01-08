
# OSPF (Open Shortest Path First)

**See**: [[Open Shortest Path First (OSPF)]] for comprehensive overview

## Intra-AS Routing Protocols

- [[Open Shortest Path First (OSPF)]] - [[Link-State Routing Protocols|link-state]]
- RIP - distance-vector
- EIGRP - hybrid
- IS-IS - [[Link-State Routing Protocols|link-state]]

## OSPF Cost Calculation

$$
\text{OSPF Cost} = \frac{\text{Reference Bandwidth (default 100 Mbps)}}{\text{Interface Bandwidth}}
$$

**Examples**:
- 10 Mbps interface: Cost = 100 / 10 = 10
- 100 Mbps interface: Cost = 100 / 100 = 1
- 1 Gbps interface: Cost = 100 / 1000 = 0.1 (rounds to 1)

## Router Types

- **[[Designated Router (DR)]]**: Elected on broadcast networks (Ethernet)
- **[[Backup Designated Router (BDR)]]**: Backup to DR  
- **[[Area Border Router (ABR)]]**: Connects multiple areas; summarizes routes
- **[[Autonomous System Boundary Router (ASBR)]]**: Gateway to external networks

## Network Types

- **[[Broadcast Multiple Access (BMA)]]**: Ethernet (DR/BDR election)
- **Point-to-Point**: Serial links (no DR/BDR)
- **Point-to-Multipoint**: Hybrid; no DR/BDR
- **NBMA**: Frame Relay/ATM (manual neighbors + DR/BDR)

## Key Concepts

### [[Link-State Database (LSDB)]]
- Topology table maintained by each router
- All routers in area have identical LSDB
- Input for SPF (Dijkstra) calculation

### Hierarchy

- **Backbone (Area 0)**: Mandatory; best practice for core
- **[[Autonomous System (AS)]]**: Entire OSPF network domain under single administration
- **Areas**: Logical grouping within AS for scalability

