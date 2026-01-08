# Exterior Gateway Protocols (EGP)

Routing protocols that exchange routing information **between** different [[Autonomous System (AS)|autonomous systems (ASes)]]. EGPs handle inter-AS routing across the internet.

## Characteristics

- **Scope**: Between autonomous systems
- **Scale**: Internet-wide routing
- **Policy-Driven**: Emphasize routing policy over shortest path
- **Scalability**: Designed for thousands/millions of routes
- **Slower Convergence**: Less frequent updates than IGPs

## Primary EGP

### BGP (Border Gateway Protocol)
- **Current Standard**: BGP4 (RFC 4271)
- **Use**: Internet backbone, ISP peering, enterprise multi-AS networks
- **Attributes**: Flexible routing decisions via path attributes (AS PATH, MED, LOCAL PREF)
- **Scalability**: Handles full internet routing table (~800K routes)

## EGP vs. [[Interior Gateway Protocols (IGPs)|IGP]]

| Feature | EGP (BGP) | IGP (OSPF, RIP) |
|---------|-----------|---|
| Scope | Between ASes | Within AS |
| Routers | [[Autonomous System Boundary Router (ASBR)]] | All internal routers |
| Metric | Path attributes, policy | Cost, hop count |
| Convergence | Slower (policy delays) | Fast |
| Scale | Internet (millions) | Enterprise (thousands) |
| Update Type | Path updates | Topology/distance updates |
| Update Frequency | Slower; event-driven | Periodic + triggered |

## Typical Deployment

```
ISP Network (AS 65000)
├─ Internal: OSPF (IGP)
└─ Edge: BGP (EGP)
        │
        ├─ BGP Peer ──→ Peer ISP (AS 65001)
        ├─ BGP Peer ──→ Another ISP (AS 65002)
        └─ BGP Peer ──→ Customer AS (AS 65003)
```

## Routing Hierarchy

1. **Within AS**: OSPF/RIP/EIGRP determines best internal paths
2. **Between AS**: BGP determines which AS to route traffic through
3. **Inside destination AS**: OSPF (or other IGP) again finds final path

## Historical Context

- **EGP (Exterior Gateway Protocol)**: Original protocol (obsolete)
- **BGP (Border Gateway Protocol)**: Replacement (1989-present)
  - BGPv1-3: Earlier versions
  - BGPv4 (1995): Current standard; supports CIDR

## Related Concepts

- [[Autonomous System (AS)]]
- [[Interior Gateway Protocols (IGPs)]]
- [[Autonomous System Boundary Router (ASBR)]]
- [[Area Border Router (ABR)]]
- [[Open Shortest Path First (OSPF)]]
