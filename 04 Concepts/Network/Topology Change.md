# Topology Change

Event in [[STP]]/[[RSTP]]/[[MSTP]] when network topology changes (link up/down, port state change), triggering reconvergence and [[MAC Address Table (CAM)]] flush.

## Triggers

**Root Cause Categories:**

### Link-Level Changes
- **Link down**: Port goes down (cable unplugged, interface admin shutdown, remote switch powered off)
- **Link up**: Port comes up (cable plugged in, interface admin startup, remote switch powered on)

### Port State Changes
- **Port transitions to forwarding**: Previously blocked/learning port now forwarding
- **Port transitions away from forwarding**: Previously forwarding port now blocking/learning
- Triggered by STP reconvergence (root election, path recalculation)

### Administrative Changes
- **Port disabled**: `shutdown` command on Huawei VRP interface
- **Port enabled**: `undo shutdown`
- **STP disabled/enabled**: `stp enable` / `undo stp enable`
- **Port added to STP domain**: Port joined spanning tree

### Switch-Level Changes
- **New switch added**: Switch joins topology
- **Switch removed/failed**: Switch disconnected or crashed
- **Root bridge elected**: Highest priority switch failed; new root takes over
- **[[Root Port]] changed**: Faster path to root discovered; original [[Root Port]] blocked

### Detailed Fault Scenarios

| Scenario | Cause | TCN Generated? | Impact |
|----------|-------|----------------|--------|
| Cable pulled from port | Link failure | Yes | Port down; [[MAC Address Table (CAM)]] aged; redirected traffic |
| Cable plugged back | Link recovery | Yes | Port up; STP may unblock; [[MAC Address Table (CAM)]] rebuilt |
| Switch power failure | [[Root Bridge]] unreachable | Yes | Multiple TC: new root elected, ports reblock |
| Upstream switch fails | [[Root Port]] lost | Yes | Alternate [[Root Port]] promoted; blocking ports unblock |
| Datacenter upgrade | Switch software upgrade | Yes | Brief loop risk if STP misconfigured |
| Rogue switch inserted | New switch joins | Yes | Topology reshape; possible new root; chaos if priorities misconfigured |

## STP (802.1D) Behavior

### Detection
1. Switch detects topology change (port goes down or new link comes up)
2. Sends **TCN [[BPDU]]** (Topology Change Notification) upstream via [[Root Port]]

### Propagation
1. TCN propagates hop-by-hop to [[Root Bridge]]
2. Each switch acknowledges with **TCA** (Topology Change Acknowledgment)
3. Root bridge receives TCN, sets **TC flag** in Configuration BPDUs
4. TC flag floods to all switches

### MAC Table Flush
- **All switches flush MACs** (except port where change occurred)
- **Forward Delay timer** reduces from 300s to 15s (accelerates MAC re-learning)
- **Disruptive**: Temporary flooding of unicast traffic

### Duration
- **35 seconds**: Topology Change flag sent (Max Age 20s + Forward Delay 15s)
- During this time, switches aggressively age MACs

## RSTP (802.1w) Behavior

### Detection
- Switch detecting change sends **TC BPDU** (not TCN; different mechanism)
- No need to wait for root acknowledgment

### Propagation
- **TC BPDU sent on all designated and root ports** (immediate propagation)
- Does not propagate to entire network; only affected switches

### MAC Table Flush
- **Only affected switches flush MACs** (more granular than STP)
- **Only non-edge ports** flush (edge ports retain MACs)
- **Less disruptive**: Smaller scope

### Duration
- **Hello Time * 2** (typically 4 seconds): TC While timer
- Faster recovery than STP

## Topology Change Impact

### Positive
- Network adapts to failures/changes
- Loop prevention maintained
- Automatic recovery

### Negative
- Temporary flooding: Unknown unicast traffic floods until MACs re-learned
- Increased CPU load: Switches recalculate STP, process BPDUs
- Potential packet loss: Brief disruption during convergence

## Minimizing Disruption

### Use RSTP/MSTP
- Faster convergence
- More granular MAC flush

### Configure Edge Ports
```
interface GigabitEthernet0/0/1
  stp edged-port enable
```
- Edge ports don't trigger topology changes
- Immediate forwarding (no delay)

### Root Guard
```
interface GigabitEthernet0/0/10
  stp root-protection
```
- Prevents unauthorized switches from becoming root
- Prevents unintended topology changes

### Loop Guard
```
interface GigabitEthernet0/0/24
  stp loop-protection
```
- Prevents alternate/root ports from becoming designated (if BPDUs stop)
- Avoids loops during BPDU loss

### BPDU Guard
```
interface GigabitEthernet0/0/1
  stp bpdu-protection
```
- Shuts down port if BPDU received (edge port protection)
- Prevents rogue switches

## Verification

```
display stp
  - Topology Change Count: Shows number of TCs
  - Time Since Last TC: Seconds since last change

display stp interface GigabitEthernet0/0/1
  - TC events on specific port
```

## Related Concepts

- [[STP]]
- [[RSTP]]
- [[MSTP]]
- [[BPDU]]
- [[Root Bridge]]
- [[MAC Address Table (CAM)]]
- [[Flooding]]
