# TCN BPDU (Topology Change Notification BPDU)

[[STP]] message sent upstream toward [[Root Bridge]] when a switch detects a [[Topology Change]]. Triggers [[MAC Address Table (CAM)]] flush and network reconvergence.

## Purpose

Signal to [[Root Bridge]] that network topology changed (link up/down, port state transition, switch added/removed) so the root can:
1. Flush its own [[MAC Address Table (CAM)]]
2. Notify all switches via TC flag in Configuration BPDUs
3. Accelerate MAC re-learning (shorten Forward Delay timer)

Without TCN, [[MAC Address Table (CAM)]] might contain stale entries for hosts that moved to different ports/VLANs.

## Fault Causes (When TCN is Generated)

### Port State Transitions
- **Port goes DOWN**: Forwarding → Blocking (link failure, admin shutdown)
- **Port comes UP**: Down → Forwarding (link recovery, admin startup)
- **Blocking → Learning → Forwarding**: Port unblocked during convergence

### Link-Level Events
- **Cable unplugged**: Immediate port down; TCN sent
- **Cable plugged**: Immediate port up; TCN sent
- **Transceiver failure**: Port physical error; treated as down
- **Remote switch powered off**: Link down on both ends; local port down; TCN sent
- **Remote switch powered on**: Link up; TCN sent

### Switch-Level Events
- **[[Root Bridge]] failed**: Entire topology reshapes; multiple TC events
- **New [[Root Bridge]] elected**: [[Root Port]]s change; ports reblock; multiple TCNs
- **[[Root Port]] changed**: Faster path to root found; old [[Root Port]] blocked; TCN sent
- **Switch added to network**: New switch joins STP; ports transition; TCN sent
- **Switch removed/crashed**: Connected switches see link failure; multiple TCNs

### Administrative Events
- **Port shutdown**: `shutdown` command; port down; TCN sent
- **Port startup**: `undo shutdown`; port up; TCN sent
- **VLAN membership change**: Port added/removed from VLAN; TCN sent
- **Spanning tree recalculation**: `stp priority` change causes port reblocking; TCN sent

### Root Port Loss
- **Upstream switch disconnected**: [[Root Port]] goes down; alternate port promoted; old [[Root Port]] blocked; TCN sent
- **Upstream link failure**: [[Root Port]] becomes non-viable; switch selects new [[Root Port]]; TCN sent
- **BPDUs from root cease**: 3 missed BPDUs (6+ seconds in RSTP); [[Root Port]] ages; port blocked; TCN sent

## TCN BPDU Frame Structure

**Minimal BPDU format** (unlike Configuration BPDU):
- **Destination MAC**: 01:80:C2:00:00:00 (Bridge Group Address)
- **BPDU Type**: 0x80 (TCN)
- **Flags**: Minimal (only topology change bit set)
- **Payload**: ~4-7 bytes (much smaller than Config BPDU)

No Root Bridge ID, path cost, timers—just "topology changed" signal.

## STP (802.1D) TCN Processing

### Detection Phase
1. Switch detects topology change (port transitions to/from forwarding)
2. **Sets TC While timer** = Max Age + Forward Delay = 35 seconds
3. **Sends TCN BPDU** out [[Root Port]] (via Designated Port upstream)

### Propagation Phase
1. Upstream switch receives TCN BPDU on **designated port**
2. Upstream switch:
   - **Acknowledges**: Sends TCA (Topology Change Acknowledgment) flag back
   - **Relays**: Sends own TCN BPDU toward root
3. Process repeats hop-by-hop until [[Root Bridge]] receives TCN

### Root Handling Phase
1. [[Root Bridge]] receives TCN
2. **Sets TC flag** in Configuration BPDU header
3. **Floods to all ports**: Sends TC-flagged Config BPDUs every 2 seconds (Hello Time)
4. **Duration**: 35 seconds (Max Age + Forward Delay)

### MAC Flush Phase
- **All non-root switches** receiving TC-flagged BPDU flush their [[MAC Address Table (CAM)]]
- **Forward Delay accelerated**: 300-second default aging → 15-second accelerated aging
- **Result**: Temporary unknown unicast flooding; MACs re-learned quickly

**Total convergence**: 50+ seconds (hop-by-hop propagation × 20s Max Age + processing delays)

## RSTP (802.1w) TCN Improvement

### Faster Propagation
- **No root dependency**: Any switch can originate TC BPDU
- **Sent on all designated/root ports**: Not just toward root
- **Immediate propagation**: Doesn't wait for root acknowledgment

### Localized Impact
- **Affected switches only**: Not entire network
- **Edge ports excluded**: Non-edge ports flush MACs; edge ports (with hosts) keep entries
- **Granular**: Switch with failed port flushes; root doesn't flush unnecessarily

### Faster Convergence
- **TC While timer**: 2 × Hello Time (typically 4 seconds) instead of 35 seconds
- **Result**: <5-second convergence vs. 50+ seconds in STP

## MSTP (802.1s) TCN Behavior

- **Per-instance**: TC flooding per MSTI (Multiple Spanning Tree Instance)
- **Region boundary**: CST (Common Spanning Tree) carries TC between regions
- **Combination of STP + RSTP**: Depends on configuration
- **Most common**: RSTP-like localized TC within regions

## Common Fault Scenarios

### Scenario 1: Link Failure
```
Timeline:
T=0s: Cable unplugged
T=0s: Port goes down; TCN sent upstream
T=0s-2s: TCN propagates to root (depends on hops)
T=2s: Root receives TCN; sets TC flag
T=2s-4s: TC flag floods to all switches
T=4s+: All switches flush MACs; unknown unicast floods
T=35s: TC flag expires; normal operation resumes

STP impact: 50+ seconds; traffic disruption possible
RSTP impact: <5 seconds; minimal disruption
```

### Scenario 2: Root Bridge Failure
```
Timeline:
T=0s: Root power failure
T=0s: All root's ports go down; multiple TC events
T=0s-20s: Root not sending BPDUs; other switches detect via Max Age
T=20s: New root elected; [[Root Port]]s recalculated; ports reblock
T=20s-50s: Topology reshapes; multiple TCs propagate
T=50s+: New topology stable

STP impact: 50+ seconds; complete topology change; major disruption
RSTP impact: <5 seconds per port transition; much faster
```

### Scenario 3: Rogue Switch Injected
```
Timeline:
T=0s: New switch plugged in; joins STP domain
T=0s: New switch exchanges BPDUs; topology reshapes
T=1s: Possibly elected as new [[Root Bridge]] (if priority low)
T=1s+: Multiple TCNs; all switches recalculate
T=50s+: Convergence; traffic redirected through rogue switch (bad!)

Prevention: Use [[STP Protection Mechanisms]] (Root Guard on uplinks)
```

## Minimizing TCN Impact

### Design Best Practices
- **Minimize port transitions**: Avoid frequent link up/down
- **Stable root**: Place root on core; configure priority 0-8192
- **Stable [[Root Port]]s**: Avoid path cost changes; stable uplinks

### Use RSTP/MSTP
```
[Huawei] stp mode rstp
```
- Faster convergence
- Localized MAC flush
- Proposal/Agreement rapid reconvergence

### Configure Edge Ports
```
interface GigabitEthernet0/0/5
  stp edged-port enable
```
- Edge ports (host-facing) don't generate TCNs
- Immediate forwarding; no delay

### Root Guard on Access Links
```
interface GigabitEthernet0/0/24
  stp root-protection
```
- Prevents unauthorized root election
- Prevents unintended topology changes

### BPDU Guard + Edge Port
```
interface GigabitEthernet0/0/10
  stp edged-port enable
  stp bpdu-protection
```
- Rogue switch detected immediately
- Port disabled; no topology disruption

## Verification

```
# Check for TCN activity
display stp | include "Topology Changes"

# Monitor specific interface
display stp interface GigabitEthernet0/0/1
  - TCN BPDUs sent
  - TCN BPDUs received
  - Topology changes

# View STP timers
display stp | include "Hello|Max Age|Forward"
```

## Related Concepts

- [[STP]]
- [[BPDU]]
- [[Topology Change]]
- [[Root Bridge]]
- [[Root Port]]
- [[MAC Address Table (CAM)]]
- [[RSTP]]
- [[STP Protection Mechanisms]]
