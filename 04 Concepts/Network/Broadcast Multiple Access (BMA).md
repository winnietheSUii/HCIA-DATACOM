# Broadcast Multiple Access (BMA)

A network type where multiple routers/devices can transmit simultaneously on a shared medium (like Ethernet) and all devices receive broadcasts. Also called **multi-access broadcast networks**.

## Characteristics

- **Multiple Devices**: Multiple routers on the same segment (LAN)
- **Broadcast Capability**: Frame sent to one address reaches all devices
- **Shared Medium**: All devices compete for bandwidth
- **Low Cost**: Inexpensive to deploy (Ethernet switches, hubs)

## Common Examples

- **Ethernet LANs**: Switched or bridged networks (most common)
- **Legacy Media**: Hubs, coaxial cable (less common now)
- **Token Ring**: Historical multi-access network
- **FDDI**: Fiber Distributed Data Interface (historical)

## OSPF Behavior on BMA Networks

### DR/BDR Election
- [[Open Shortest Path First (OSPF)]] elects [[Designated Router (DR)]] and [[Backup Designated Router (BDR)]]
- Reduces adjacencies and flooding overhead

### Hello Mechanism
- Routers send Hello packets to **224.0.0.5** (All SPF Routers multicast)
- All routers on segment hear Hellos
- Establishes bidirectional communication (2-Way state)

### Flooding Optimization
- **All routers → DR**: Send LSAs to **224.0.0.6** (All Designated Routers multicast)
- **DR → All routers**: Floods LSAs to **224.0.0.5**
- Significantly reduces duplicate LSA copies

## Adjacency Model on BMA

```
Network Topology:
R1 --- Ethernet Segment --- R2
  |                          |
  +------ R3 (DR)  --------- +
         R4 (BDR)

Adjacencies:
- R3 (DR) ↔ R1, R2, R4  [Full]
- R4 (BDR) ↔ R1, R2, R3 [Full]
- R1 ↔ R2 [2-Way, not Full]
- R1 ↔ R4 [2-Way, not Full]
```

## Advantages

- **Efficiency**: Single physical segment serves multiple routers
- **Cost**: Low infrastructure cost
- **Reduced Latency**: Direct frame delivery
- **Shared Bandwidth**: Good for small segments

## Disadvantages

- **Collision Risk**: On legacy shared media (now obsolete with switches)
- **Bandwidth Limits**: Multiple devices compete for bandwidth
- **Security**: Broadcast packets seen by all devices (containable with VLANs)
- **Scalability**: Large numbers of routers on single segment become difficult

## vs. Point-to-Point Networks

| Aspect      | BMA                | Point-to-Point            |     |
| ----------- | ------------------ | ------------------------- | --- |
| Devices     | Multiple           | Two                       |     |
| DR/BDR      | Yes                | No                        |     |
| Adjacencies | $n(n+1)/2$         | 1                         |     |
| Typical Use | Ethernet LANs      | Serial links, GRE tunnels |     |
| Efficiency  | High (with DR/BDR) | Direct; no overhead       |     |

## Related Concepts

- [[Designated Router (DR)]]
- [[Backup Designated Router (BDR)]]
- [[Open Shortest Path First (OSPF)]]
- [[Network-Topology]]
