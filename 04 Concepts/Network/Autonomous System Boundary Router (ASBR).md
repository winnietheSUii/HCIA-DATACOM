# Autonomous System Boundary Router (ASBR)

A router that connects an [[Autonomous System (AS)]] (running [[Open Shortest Path First (OSPF)]] or other [[Interior Gateway Protocols (IGPs)|IGP]]) to external autonomous systems, typically using an [[Exterior Gateway Protocols (EGP)]] like BGP.

## Purpose

- **AS Connectivity**: Peers with routers in other autonomous systems
- **Route Injection**: Imports external routes from peer ASes into local OSPF
- **Policy Application**: Applies routing policy (filtering, prepending, etc.)
- **Multi-AS Networks**: Enables enterprise/ISP networks spanning multiple ASes

## Placement in Network

```
Enterprise Network:
AS 65001 (OSPF)
├─ Internal Router
├─ ABR (Area 1 ← → Area 0)
└─ ASBR [Router ID 3.3.3.3]
        │
        └─ BGP Peer ─── ASBR (AS 65002)
                         │
                      [RIP/OSPF]
                      AS 65002
```

## Key Responsibilities

### 1. External Connectivity
- Runs [[Interior Gateway Protocols (IGPs)|IGP]] (OSPF) internally
- Runs [[Exterior Gateway Protocols (EGP)]] (usually BGP) to peer ASes
- Translates between internal and external routing

### 2. Route Injection
- Learns routes from external ASes via BGP
- Converts external routes to OSPF External LSAs (Type 5 or Type 7)
- Advertises external routes to all internal routers via OSPF

### 3. Default Route Advertisement
- Often advertises default route (0.0.0.0/0) to internal routers
- Directs unknown traffic toward external networks

### 4. Policy Implementation
- **Route filtering**: Block/accept specific external routes
- **Metric setting**: Assign preference to routes
- **Prepending**: Manipulate path cost to external ASes
- **Redistribution**: Convert between IGP and EGP metrics

## OSPF External Route Types

### Type 1 (E1)
- Metric = ASBR cost + external route cost
- Used when ASBR reachability matters

### Type 2 (E2) - Default
- Metric = external route cost only (ASBR cost ignored)
- Simpler; preferred in most networks

## Configuration Example

```
! ASBR running OSPF internally and BGP externally

ospf 1
  area 0
    network 192.168.0.0 0.0.0.255

bgp 65001
  neighbor 10.1.1.1 remote-as 65002
  !
  ! Redistribute BGP routes into OSPF
  redistribute bgp 65001 into ospf 1 cost 100 type 1
  !
  ! Redistribute OSPF routes into BGP
  redistribute ospf 1 into bgp 65001
```

## ASBR vs. ABR

| Feature | ASBR | ABR |
|---------|------|-----|
| AS Scope | Connects to external ASes | Within single AS |
| Protocol | IGP + EGP (e.g., OSPF + BGP) | Single IGP (OSPF) |
| Route Type | External routes | Inter-area routes |
| LSA Type | Type 5/7 (External) | Type 3 (Summary) |
| Example | ISP router, enterprise gateway | Multi-area OSPF router |

## Importance in Networks

- **Internet Connectivity**: Gateway to internet and peer ASes
- **Multi-Vendor Support**: Often bridge between different routing domains
- **Policy Control**: Implements organizational routing policies
- **Redundancy**: Multiple ASBRs enable failover to alternate paths

## Related Concepts

- [[Autonomous System (AS)]]
- [[Area Border Router (ABR)]]
- [[Open Shortest Path First (OSPF)]]
- BGP (Border Gateway Protocol)
- External routes and route redistribution
