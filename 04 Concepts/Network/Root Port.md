# Root Port

The port on a non-root switch with the best path to the [[Root Bridge]] in [[STP]]/[[RSTP]].

## Characteristics

- **One per non-root switch**: Each switch has exactly one root port
- **Always forwarding**: Receives/sends data
- **Lowest root path cost**: Sum of costs to reach root
- **Receives BPDUs**: From designated port on upstream switch

## Selection Criteria (Priority Order)

When multiple ports could be root port:

1. **Lowest root path cost** to root bridge
2. **Lowest sender Bridge ID** (upstream switch)
3. **Lowest sender port priority** (upstream port)
4. **Lowest sender port ID** (upstream port number)

## Root Path Cost Calculation

- **Root bridge's perspective**: Root path cost = 0
- **Downstream switch**: Root path cost = Received cost + local port cost
- Accumulates as BPDUs propagate away from root

Example:
```
Root Bridge (cost 0)
  ↓ 1G link (cost 20,000)
Switch A (root path cost = 20,000)
  ↓ 1G link (cost 20,000)
Switch B (root path cost = 40,000)
```

## Root Port on [[Root Bridge]]

- Root bridge has **no root port**
- All root bridge ports are [[Designated Port]]s

## Configuration (Huawei VRP)

```
# Manually adjust port cost to influence root port selection
interface GigabitEthernet0/0/1
  stp cost 10000   ; Lower cost = preferred

# Set port priority (rarely used)
interface GigabitEthernet0/0/2
  stp port-priority 64
```

## Failure Handling

- If root port fails, switch recalculates
- [[Alternate Port]] (RSTP) becomes new root port (rapid)
- Classic STP: Non-designated port transitions to root port (50 seconds)

## Verification

```
display stp interface GigabitEthernet0/0/1
  - Role: Root Port
  - State: Forwarding
  - Path Cost: 20,000
```

## Related Concepts

- [[STP]]
- [[RSTP]]
- [[Root Bridge]]
- [[Designated Port]]
- [[Alternate Port]]
- [[Path Cost]]
