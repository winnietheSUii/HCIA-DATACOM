# Path Cost

Metric used by [[STP]]/[[RSTP]] to calculate the best path to the [[Root Bridge]]. Lower cost = preferred path.

## Cost Values

### 802.1D-1998 (Original)

| Link Speed | Cost |
|------------|------|
| 10 Mbps | 100 |
| 100 Mbps | 19 |
| 1 Gbps | 4 |
| 10 Gbps | 2 |

**Issue**: Doesn't scale well for speeds >10G (minimum cost = 1).

### 802.1D-2004 (Long Path Cost)

| Link Speed | Cost |
|------------|------|
| 10 Mbps | 2,000,000 |
| 100 Mbps | 200,000 |
| 1 Gbps | 20,000 |
| 10 Gbps | 2,000 |
| 100 Gbps | 200 |

**Formula**: `Cost = 20,000,000,000 / bandwidth (bps)`

Huawei default uses 802.1D-2004 (long cost).

## Root Path Cost Calculation

- **Root bridge**: Root path cost = 0
- **Downstream switch**: Root path cost = Received cost + local ingress port cost

**Example**:
```
Root Bridge (cost 0)
  ↓ BPDU sent (cost 0)
Switch A receives on 1G port (cost 20,000)
  → SW-A root path cost = 0 + 20,000 = 20,000
  ↓ BPDU relayed (cost 20,000)
Switch B receives on 1G port (cost 20,000)
  → SW-B root path cost = 20,000 + 20,000 = 40,000
```

## Manual Cost Configuration

```
interface GigabitEthernet0/0/1
  stp cost 10000   ; Override default cost
```

**Use cases**:
- Prefer specific path to root
- Influence [[Root Port]] selection
- Load balancing across parallel links

## Cost Method Configuration

```
# Switch between short (1998) and long (2004) cost
stp pathcost-standard legacy     ; 802.1D-1998
stp pathcost-standard dot1t      ; 802.1D-2004 (default)
```

## Impact on Topology

- Lower total cost path → becomes [[Root Port]]
- Equal cost paths → tie-break by Bridge ID, port priority, port ID
- Higher cost path → blocked (non-designated or [[Alternate Port]])

## Verification

```
display stp interface GigabitEthernet0/0/1
  - Port path cost: 20,000
  - Designated path cost: 40,000
```

## Related Concepts

- [[STP]]
- [[RSTP]]
- [[Root Port]]
- [[Designated Port]]
- [[Root Bridge]]
