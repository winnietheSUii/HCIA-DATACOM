# STP (Spanning Tree Protocol)

IEEE 802.1D protocol that prevents layer 2 loops in [[Ethernet Switch]] networks by creating a loop-free logical topology.

## The Problem: Layer 2 Loops

Without STP, redundant switch links cause:
- **[[Broadcast Storm]]**: Broadcasts loop infinitely, consuming bandwidth
- **MAC table instability**: Switches see same [[MAC Address]] on multiple ports (MAC flapping)
- **Duplicate frames**: Hosts receive multiple copies of same frame
- **Network failure**: CPU overload, congestion, unreachability

## How STP Works

1. **Election**: Switches elect one [[Root Bridge]] (lowest Bridge ID)
2. **Path calculation**: Each switch calculates shortest path to root
3. **Port roles**: Assign roles based on path cost
4. **Block ports**: Place redundant ports in blocking state
5. **Loop-free topology**: Only one active path between any two switches

## Bridge ID

Used to elect root bridge and break ties:
- **Priority** (2 bytes): 0-61440, default 32768, increments of 4096
- **MAC Address** (6 bytes): Switch's MAC (tiebreaker)
- **Format**: Priority.MAC (e.g., 32768.00-11-22-33-44-55)

Lower Bridge ID wins; priority adjusted first, MAC is tiebreaker.

## Port Roles

### [[Root Port]] (RP)
- Best path to [[Root Bridge]]
- One per non-root switch
- Always forwarding

### [[Designated Port]] (DP)
- Best path to root from segment's perspective
- One per LAN segment
- Always forwarding
- All ports on root bridge are DPs

### [[Alternate Port]] (RSTP)
- Backup path to root
- Blocking/discarding state

### [[Backup Port]] (RSTP)
- Backup for same segment
- Rare; occurs with hubs

### Non-Designated Port (Legacy STP)
- Not root, not designated
- Blocking state (prevents loops)

## Port States (802.1D STP)

1. **Disabled**: Administratively down
2. **Blocking**: Receives BPDUs only; 20 seconds
3. **Listening**: Elects root; sends/receives BPDUs; 15 seconds
4. **Learning**: Learns MAC addresses; 15 seconds
5. **Forwarding**: Full operation

**Convergence time**: 50 seconds (20 + 15 + 15)

## Path Cost

Metric for selecting best path to root:

| Speed | 802.1D-1998 Cost | 802.1D-2004 Cost |
|-------|------------------|------------------|
| 10 Mbps | 100 | 2,000,000 |
| 100 Mbps | 19 | 200,000 |
| 1 Gbps | 4 | 20,000 |
| 10 Gbps | 2 | 2,000 |
| 100 Gbps | N/A | 200 |

Root path cost = Sum of costs on path to root

## BPDU (Bridge Protocol Data Unit)

Messages exchanged between switches:
- **Configuration BPDU**: Root election, path cost, topology info
- **TCN BPDU** (Topology Change Notification): Signals topology change
- Sent every 2 seconds (Hello Time)

## STP Timers

- **Hello Time**: 2 seconds (BPDU transmission interval)
- **Forward Delay**: 15 seconds (listening/learning duration)
- **Max Age**: 20 seconds (BPDU aging; missing BPDUs trigger reconvergence)

**Total convergence**: 50 seconds (blocking + listening + learning)

## STP Variants

- **STP (802.1D)**: Original; 50-second convergence
- **[[RSTP]] (802.1w)**: Rapid; <5-second convergence
- **[[MSTP]] (802.1s)**: Multiple instances for different VLANs
- **PVST+** (Cisco): Per-VLAN STP instance

## Advantages

- Automatic loop prevention
- Redundancy without manual intervention
- Self-healing on topology changes

## Disadvantages

- Slow convergence (classic STP)
- Blocks links (wastes bandwidth; use RSTP/MSTP + link aggregation)
- CPU overhead for BPDU processing

## Configuration (Huawei VRP)

```
# Enable STP globally (usually enabled by default)
stp enable

# Set STP mode
stp mode stp      ; Classic 802.1D
stp mode rstp     ; Rapid Spanning Tree
stp mode mstp     ; Multiple Spanning Tree

# Set bridge priority (lower = preferred root)
stp priority 4096

# Set port cost
interface GigabitEthernet0/0/1
  stp cost 20000
```

## Related Concepts

- [[Root Bridge]]
- [[Root Port]]
- [[Designated Port]]
- [[BPDU]]
- [[RSTP]]
- [[MSTP]]
- [[Broadcast Storm]]
- [[Ethernet Switch]]
