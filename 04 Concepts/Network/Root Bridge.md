# Root Bridge

The switch elected as the root of the spanning tree in [[STP]] or [[RSTP]]. All other switches calculate paths to reach the root bridge.

## Election Criteria

1. **Lowest Bridge ID wins**
   - Bridge ID = Priority (2 bytes) + MAC Address (6 bytes)
   - Default priority: 32768 (adjustable in increments of 4096)
   - If priorities tie, lowest MAC wins

2. **Once elected**:
   - Root bridge sends Configuration [[BPDU]]s from all ports
   - Other switches relay BPDUs with increasing cost
   - Remains root unless lower Bridge ID appears or root fails

## Root Bridge Characteristics

- **All ports are [[Designated Port]]s**: Always forwarding
- **No [[Root Port]]**: Root doesn't need path to itself
- **BPDU origination**: Only root generates original BPDUs
- **Stable position**: Center of spanning tree topology

## Best Practices

### Manual Root Placement

- Place root at network core (high bandwidth, redundant links)
- Avoid edge switches as root
- Set priority to ensure desired root:

```
# Primary root
[SW-Core1] stp priority 0

# Secondary root (backup)
[SW-Core2] stp priority 4096
```

### Root Guard

- Prevents unauthorized switches from becoming root
- Configured on ports where root should not appear

```
interface GigabitEthernet0/0/1
  stp root-protection
```

## Root Bridge Failure

- Switches stop receiving BPDUs from root (Max Age = 20 seconds)
- New election begins; switch with next-lowest Bridge ID becomes root
- Topology recalculates (convergence time depends on STP variant)

## Verification

```
display stp brief
display stp interface GigabitEthernet0/0/1
```

## Related Concepts

- [[STP]]
- [[RSTP]]
- [[Bridge ID]]
- [[Root Port]]
- [[Designated Port]]
- [[BPDU]]
