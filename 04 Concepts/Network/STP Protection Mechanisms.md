# STP Protection Mechanisms

Security and stability features in [[STP]]/[[RSTP]]/[[MSTP]] to prevent loops, unauthorized topology changes, and misconfigurations.

## Root Guard (Root Protection)

**Prevents**: Unauthorized switch from becoming [[Root Bridge]]

**Use case**: Ensure core switch remains root; edge switches cannot usurp

**Behavior**: If superior [[BPDU]] received (lower Bridge ID) → port goes to discarding state

```
interface GigabitEthernet0/0/10
  stp root-protection
```

**Where to enable**: Ports facing distribution/access layer (not toward expected root)

## BPDU Guard (BPDU Protection)

**Prevents**: Rogue switches on [[Edge Port]]s

**Use case**: Access ports to hosts; should never receive BPDUs

**Behavior**: If BPDU received → port err-disabled (shut down)

```
interface GigabitEthernet0/0/1
  stp bpdu-protection
```

**Where to enable**: Edge ports (PCs, printers, servers)

**Recovery**: Manual `undo shutdown` or auto-recovery after timeout (configurable)

## Loop Guard (Loop Protection)

**Prevents**: Loops caused by unidirectional link failure (BPDUs lost but link appears up)

**Use case**: Point-to-point links between switches

**Behavior**: If [[Root Port]] or [[Alternate Port]] stops receiving BPDUs → stays in blocking (doesn't become designated)

```
interface GigabitEthernet0/0/24
  stp loop-protection
```

**Where to enable**: Non-edge ports (switch-facing)

**Without loop guard**: Port could become designated (thinking upstream switch is gone) → loop

## TC Guard (Topology Change Guard)

**Prevents**: Excessive [[Topology Change]] events (DoS or flapping links)

**Use case**: Limit TC processing during instability

**Behavior**: Limits TC BPDU processing rate (e.g., max 3 TCs per 2 seconds)

```
# Huawei: TC protection threshold
stp tc-protection threshold 3 interval 2
```

**Effect**: Ignores excess TC events; prevents CPU overload

## BPDU Filter

**Function**: Suppresses BPDU transmission/reception on port

**Use case**: Rare; connecting to non-STP devices (risky; can cause loops)

```
interface GigabitEthernet0/0/5
  stp bpdu-filter enable
```

**Warning**: Port acts as if STP disabled; use cautiously (typically for connecting to single end device or isolated network)

## Comparison Table

| Protection | Prevents | Action | Enable Where |
|------------|----------|--------|--------------|
| **Root Guard** | Unauthorized root | Block port | Distribution/access facing core |
| **BPDU Guard** | Rogue switches | Err-disable port | Edge ports (hosts) |
| **Loop Guard** | Unidirectional failure loops | Keep blocking | Switch-to-switch links |
| **TC Guard** | TC flooding/DoS | Rate-limit TCs | Globally or per-port |
| **BPDU Filter** | BPDU on non-STP device | Stop BPDUs | Isolated devices (rare) |

## Best Practices

1. **Root Guard**: Enable on all ports toward access layer
2. **BPDU Guard**: Enable on all edge ports (with auto-recovery)
3. **Loop Guard**: Enable on uplinks and inter-switch links
4. **TC Guard**: Enable globally to protect from flapping
5. **BPDU Filter**: Avoid unless absolutely necessary

## Verification

```
display stp interface GigabitEthernet0/0/1
  - Root Protection: Enabled/Disabled
  - BPDU Protection: Enabled/Disabled
  - Loop Protection: Enabled/Disabled

display stp tc-protection
  - TC threshold and interval
```

## Related Concepts

- [[STP]]
- [[RSTP]]
- [[MSTP]]
- [[Root Bridge]]
- [[BPDU]]
- [[Topology Change]]
- [[Edge Port]]
