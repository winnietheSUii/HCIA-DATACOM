# Interior Gateway Protocols (IGPs)

Routing protocols used for routing **within** a single [[Autonomous System (AS)]]. IGPs handle intra-AS routing (internal network routing).

## Characteristics

- Operate within a single autonomous system
- Determine best paths to destinations within the AS
- Exchange routing information only with routers in the same AS
- Generally divide networks into subnets/areas for scalability

## Classification

### Distance-Vector IGPs
- **RIP** (Routing Information Protocol)
- **EIGRP** (Enhanced Interior Gateway Routing Protocol) - primarily intra-AS

### Link-State IGPs
- **[[Open Shortest Path First (OSPF)]]** - most common in enterprise networks
- **IS-IS** (Intermediate System to Intermediate System)

## vs. EGP (Exterior Gateway Protocols)

| Aspect | IGP | EGP |
|--------|-----|-----|
| Scope | Within AS | Between ASes |
| Example | [[Open Shortest Path First (OSPF)]], RIP | BGP |
| Frequency | Frequent updates | Less frequent |
| Scalability | 100s-1000s routers | Entire internet |

## OSPF as an IGP

- Advertises internal routes within an [[Autonomous System (AS)]]
- Uses [[Area Border Router (ABR)]] for multi-area designs
- Routes external to AS are handled by [[Autonomous System Boundary Router (ASBR)]]
