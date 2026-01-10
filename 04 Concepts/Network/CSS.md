# CSS (Cluster Switch System)

Huawei cross-device virtualization that **merges two or more core switches** into a **single logical system**. Enables **multi-chassis link aggregation** (similar to MC-LAG/VSS) for **active-active uplinks**.

## Benefits
- **No single point of failure** on uplinks
- **Active-active** links to access/distribution
- **Single management plane**
- **Fast convergence**

## Features
- **Dual-active detection**: Prevents split-brain
- **Multi-chassis aggregation**: One side can bundle links across two switches
- **Consistent config**: Syncs interfaces and policies

## Use Cases
- Core/distribution redundancy
- Datacenter ToR → Aggregation

## Verification
```
display css
```
- Shows members, role, dual-active state, consistency

## Related
- [[iStack]]
- [[Eth-Trunk]]
- [[Link Aggregation]]
