# Alternate Port

An [[RSTP]]/[[MSTP]] port role representing a backup path to the [[Root Bridge]].

## Characteristics

- **Backup [[Root Port]]**: Provides alternate path if root port fails
- **Discarding state**: Does not forward data; blocks to prevent loops
- **Rapid transition**: Can become root port in <1 second on failure
- **Receives BPDUs**: Continuously monitors alternate path

## Why Alternate Port?

In [[STP]] (802.1D), non-designated ports were simply "blocking." RSTP distinguishes:
- **Alternate Port**: Backup path to root (common)
- **[[Backup Port]]**: Backup for same segment (rare; hub scenario)

This distinction enables rapid convergence.

## Selection

Switch has multiple paths to root:
1. **Best path** → [[Root Port]] (forwarding)
2. **Second-best path** → **Alternate Port** (discarding)
3. Additional paths → also alternate ports

## Alternate Port Activation

When root port fails:
1. **RSTP detects failure** (3 missed BPDUs = 6 seconds)
2. **Alternate port immediately becomes new root port** (no timers)
3. **Converges in <1 second** (vs. 50 seconds in classic STP)

## Example Topology

```
Root Bridge
  |  \
  |   \
  |    \
 SW1---SW2
  
SW2 has two paths to root:
  - Direct link → Root Port (forwarding)
  - Via SW1 → Alternate Port (discarding)

If SW2's root port fails:
  → Alternate port becomes root port immediately
```

## Configuration

Alternate port role is automatic; influenced by:
- Bridge priority
- Port cost
- Bridge ID

```
# Lower cost on preferred path
interface GigabitEthernet0/0/1
  stp cost 10000   ; Becomes root port

# Higher cost on backup path
interface GigabitEthernet0/0/2
  stp cost 30000   ; Becomes alternate port
```

## Verification

```
display stp interface GigabitEthernet0/0/2
  - Role: Alternate Port
  - State: Discarding
  - Reason: Inferior root path cost
```

## vs. [[Backup Port]]

| Feature | Alternate Port | Backup Port |
|---------|----------------|-------------|
| Function | Backup path to root | Backup for same segment |
| Common? | Yes (redundant topology) | Rare (hubs, loopback) |
| Replaces | Root Port | Designated Port |

## Related Concepts

- [[RSTP]]
- [[MSTP]]
- [[Root Port]]
- [[Designated Port]]
- [[Backup Port]]
- [[BPDU]]
