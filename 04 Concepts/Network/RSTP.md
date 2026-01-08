# RSTP (Rapid Spanning Tree Protocol)

IEEE 802.1w enhancement to [[STP]] that provides faster convergence (<5 seconds vs. 50 seconds).

## Key Improvements

### 1. Rapid Convergence
- **Edge ports**: Transition to forwarding immediately (no delay)
- **Point-to-point links**: Proposal/Agreement handshake (no timers)
- **Topology change**: Only affects specific paths, not entire topology

### 2. New Port Roles

- **[[Root Port]]**: Best path to root (same as STP)
- **[[Designated Port]]**: Best for segment (same as STP)
- **[[Alternate Port]]**: Backup path to root (replaces non-designated)
- **[[Backup Port]]**: Backup for same segment (rare)

### 3. Port States (Simplified)

| RSTP State | STP Equivalent | Function |
|------------|----------------|----------|
| **Discarding** | Blocking, Listening, Disabled | Not forwarding |
| **Learning** | Learning | Learning MACs, not forwarding |
| **Forwarding** | Forwarding | Full operation |

No blocking/listening distinction → faster transitions.

### 4. Proposal/Agreement Mechanism

When link comes up between two switches:
1. **Downstream switch** sends Proposal [[BPDU]]
2. **Upstream switch** (closer to root) syncs (blocks other ports if needed)
3. **Upstream switch** sends Agreement BPDU
4. **Downstream port** transitions to forwarding immediately (no 30-second delay)

### 5. BPDU Handling

- **Every switch generates BPDUs** (not just relayed from root)
- **3 missed BPDUs** → declare neighbor failed (6 seconds with 2-second Hello)
- Faster failure detection than STP (20-second Max Age)

## Edge Port (PortFast)

- Port connected to end device (PC, server, printer)
- Transitions to forwarding immediately
- No [[BPDU]]s expected
- If BPDU received → becomes normal STP port

```
interface GigabitEthernet0/0/1
  stp edged-port enable
```

## Point-to-Point vs. Shared

- **Point-to-Point**: Full-duplex link (switch-to-switch); uses Proposal/Agreement
- **Shared**: Half-duplex or hub-connected; falls back to STP timers

Detection automatic based on duplex; can override:
```
interface GigabitEthernet0/0/24
  stp point-to-point force-true
```

## Topology Change (TC)

### STP Behavior
- TCN [[BPDU]] sent to root
- Root floods TC BPDU to all switches
- All switches flush [[MAC Address Table (CAM)]] (except port where TC originated)
- Disruptive; affects entire topology

### RSTP Behavior
- Switch detecting change sends TC [[BPDU]] on all designated/root ports
- Only affected switches flush MACs (not entire network)
- Faster, less disruptive

## Compatibility

- **RSTP ↔ RSTP**: Full rapid convergence
- **RSTP ↔ STP**: RSTP falls back to STP timers (50 seconds) on that port

## Configuration (Huawei VRP)

```
# Enable RSTP globally
stp mode rstp

# Set bridge priority
stp priority 4096

# Configure edge port
interface GigabitEthernet0/0/1
  stp edged-port enable
```

## Convergence Comparison

| Event | STP | RSTP |
|-------|-----|------|
| Link failure | 50 seconds | <1 second (alternate port) |
| New link | 50 seconds | <5 seconds (proposal/agreement) |
| Edge port | 50 seconds | Immediate |

## Related Concepts

- [[STP]]
- [[MSTP]]
- [[Root Bridge]]
- [[Root Port]]
- [[Designated Port]]
- [[Alternate Port]]
- [[Backup Port]]
- [[BPDU]]
- [[Topology Change]]
