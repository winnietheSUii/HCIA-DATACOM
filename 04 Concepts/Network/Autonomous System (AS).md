# Autonomous System (AS)

A collection of routers and networks under a single administrative authority that uses a single [[Interior Gateway Protocols (IGPs)|interior gateway protocol (IGP)]] for routing decisions internally.

## Characteristics

- **Single Administration**: Operated by one organization (ISP, enterprise, etc.)
- **Single IGP**: All routers use same [[Interior Gateway Protocols (IGPs)|IGP]] internally (e.g., [[Open Shortest Path First (OSPF)]], RIP, IS-IS)
- **ASN (Autonomous System Number)**: 16-bit or 32-bit unique identifier (0-65535)
- **Self-contained routing policy**: Independent routing decisions

## Why Autonomous Systems?

### Internet Scalability
- Can't store every router's topology globally (scale problem)
- ASes partition internet into manageable domains
- Routers only need to know routes within their AS + paths to other ASes

### Administrative Control
- Each AS has routing policy independent of others
- Organizations control which neighbors they peer with
- Filter or manipulate routes based on policy

## Types of Routers in AS

### Internal Router
- All interfaces are in same area (within AS)
- Runs only IGP
- Example: Core [[Open Shortest Path First (OSPF)]] router

### [[Area Border Router (ABR)]] (Multi-Area OSPF)
- Connects multiple OSPF areas within same AS
- Summarizes routes between areas
- Example: OSPF ABR connecting Area 0 and Area 1

### [[Autonomous System Boundary Router (ASBR)]]
- "Edge" router connecting to external ASes
- Runs [[Interior Gateway Protocols (IGPs)|IGP]] internally + [[Exterior Gateway Protocols (EGP)]] externally (usually BGP)
- Injects external routes into AS
- Example: ISP router running OSPF internally and BGP to peer ASes

## Routing Hierarchy

```
AS 65001 (Enterprise)
  +----- Internal Routers (OSPF)
  +
  ASBR --- BGP --- ASBR (AS 65002)
                     |
                  Internal Routers (RIP/OSPF)
```

## AS in [[Open Shortest Path First (OSPF)]]

- OSPF is **intra-AS routing protocol**
- All routers in OSPF network are in same AS
- Routes learned via OSPF stay within AS
- External routes (from other ASes) learned via [[Area Border Router (ABR)]] (ABR) or [[Autonomous System Boundary Router (ASBR)]] (ASBR)

## ASN Assignment

- **1-64511**: Public ASNs (registered with IANA/RIRs)
- **64512-65534**: Private ASNs (not routed on public internet)
- **65535**: Reserved

## Related Concepts

- [[Interior Gateway Protocols (IGPs)]]
- [[Area Border Router (ABR)]]
- [[Autonomous System Boundary Router (ASBR)]]
- [[Open Shortest Path First (OSPF)]]
- BGP (Exterior Gateway Protocol)
