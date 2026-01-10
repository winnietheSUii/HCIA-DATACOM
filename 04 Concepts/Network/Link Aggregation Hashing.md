# Link Aggregation Hashing

Method used to distribute traffic across aggregated links using a **hash function**. Ensures **per-flow load balancing** to preserve packet order.

## Per-Flow vs Per-Packet

### Per-Flow Load Balancing (Default)
- **Hash-based selection**: Uses packet fields to compute hash → selects member link
- **Flow affinity**: Same flow (5-tuple) always uses same member
- **Packet order preserved**: Critical for TCP performance
- **Distribution**: Multiple flows spread across links
- **Trade-off**: Single elephant flow limited by one member's bandwidth

### Per-Packet Load Balancing (Avoided)
- **Round-robin**: Packets distributed sequentially regardless of flow
- **Out-of-order delivery**: Same flow packets take different paths
- **TCP degradation**: Duplicate ACKs, retransmissions, throughput collapse
- **Disabled by default** on enterprise switches

## Hash Inputs

### Layer 2 (MAC-based)
- **src-mac**: Source MAC only
- **dst-mac**: Destination MAC only
- **src-dst-mac**: Both MACs (better distribution)
- **Use case**: Many different hosts; pure L2 switching

### Layer 3 (IP-based)
- **src-ip**: Source IP only
- **dst-ip**: Destination IP only
- **src-dst-ip**: Both IPs (recommended for routed traffic)
- **Use case**: Inter-VLAN routing; diverse subnets

### Layer 4 (Port-based)
- **src-dst-ip src-dst-port**: IP addresses + TCP/UDP ports
- **Highest entropy**: Best distribution for diverse flows
- **Use case**: Many concurrent sessions (web, databases, multi-tenant)

## Configuration (Huawei VRP)

```
[Switch] interface Eth-Trunk1
[Switch-Eth-Trunk1] load-balance src-dst-ip src-dst-port
```

Available policies:
- `dst-mac`
- `src-mac`
- `src-dst-mac`
- `dst-ip`
- `src-ip`
- `src-dst-ip`
- `src-dst-ip src-dst-port` (recommended)

## Hash Algorithm Details

**Hash function**: CRC-based or proprietary
- Input: Selected packet fields (MAC/IP/port)
- Output: N-bit hash → modulo number of active members → member index
- Example: 4 members → hash % 4 → link 0-3

**Flow definition** (5-tuple):
1. Source IP
2. Destination IP
3. Protocol (TCP/UDP/ICMP)
4. Source port
5. Destination port

## Imbalance Causes

| Cause | Symptom | Mitigation |
|-------|---------|------------|
| **Few large flows** | One link saturated; others idle | Add L4 ports to hash; increase members |
| **Asymmetric traffic** | Uneven src/dst distribution | Use bidirectional hash (src-dst) |
| **Hash collision** | Multiple flows hash to same link | Increase members (more bins) |
| **Single elephant flow** | One flow > member bandwidth | Traffic engineering; QoS; separate trunk |

## Verification

```
display eth-trunk load-balance
  - Shows current hashing policy

display eth-trunk statistics
  - Per-member traffic counters
  - Reveals imbalance (one link much higher)
```

## Best Practices

1. **Include L4 ports** for diverse flows (web, apps)
2. **Use src-dst** (bidirectional) for symmetric traffic
3. **Monitor utilization** per member; detect imbalance
4. **Plan for elephant flows**: Separate trunks or traffic engineering
5. **Avoid per-packet** unless very specific non-TCP use case

## Related

- [[Link Aggregation]]
- [[Eth-Trunk]]
- [[LACP]]
- [[Load Balancing]]
