# Eth-Trunk (Link Aggregation)

Huawei term for **link aggregation** bundling multiple physical Ethernet links into **one logical interface** to provide **higher bandwidth**, **redundancy**, and **load balancing**.

## Benefits
- **Reliability**: If one member link fails, traffic continues on others
- **Bandwidth expansion**: Aggregate throughput (e.g., 4 x 1G ≈ 4 Gbps)
- **Load balancing**: Per-flow hashing over member links
- **Fast recovery**: No spanning tree blocking within trunk

## Components
- **Eth-Trunk interface**: Logical L2/L3 interface (e.g., Eth-Trunk1)
- **Member links**: Physical ports added to the trunk
- **Mode**: [[Manual Link Aggregation]] (static) or [[LACP]] (dynamic)
- **Hash policy**: [[Link Aggregation Hashing]] (src/dst MAC/IP/port)

## Failure Handling
- Member link down → removed from trunk → hash recalculated → flows redistributed
- All links down → trunk down

## Verification
```
display eth-trunk
```
- Shows trunk state, members, mode, load-balance policy

## Related Concepts
- [[Link Aggregation]]
- [[LACP]]
- [[Manual Link Aggregation]]
- [[Link Aggregation Hashing]]
- [[iStack]]
- [[CSS]]
