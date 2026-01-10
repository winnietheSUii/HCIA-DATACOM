# Module 9: STP Principles and Configuration

## 9.1 STP Overview

[[STP]] (Spanning Tree Protocol, IEEE 802.1D) prevents layer 2 loops in redundant [[Ethernet Switch]] topologies by creating a loop-free logical topology.

**The Loop Problem:**
- Redundant links provide failover but create forwarding loops
- Without loop prevention:
  - **[[Broadcast Storm]]**: Broadcasts loop infinitely, consuming all bandwidth
  - **MAC table instability**: [[MAC Address Table (CAM)]] flaps (same MAC seen on multiple ports)
  - **Duplicate frames**: Hosts receive multiple copies
  - **Network failure**: CPU overload, timeouts, unreachability

**STP Solution:**
- Automatically blocks redundant ports to prevent loops
- Maintains single active path between switches
- Unblocks ports when primary path fails (failover)
- Self-healing: Adapts to topology changes

**STP Variants:**
- **STP (802.1D)**: Original; 50-second convergence
- **[[RSTP]] (802.1w)**: Rapid; <5-second convergence
- **[[MSTP]] (802.1s)**: Multiple instances for [[VLAN]] load balancing
- **PVST+** (Cisco proprietary): Per-VLAN STP

## 9.2 Basic Concepts and Working Mechanism of STP

### 9.2.1 STP Basic Concepts

**[[Bridge ID]]:**
- Unique identifier = **Priority (2 bytes) + MAC Address (6 bytes)**
- Format: 32768.00-11-22-33-44-55
- Priority: 0-61440, default 32768, multiples of 4096
- Lower Bridge ID preferred (for root election, tie-breaking)

**[[Root Bridge]]:**
- Switch with lowest [[Bridge ID]]
- Center of spanning tree topology
- All ports are [[Designated Port]]s (forwarding)
- Originates Configuration [[BPDU]]s every 2 seconds

**Port Roles:**

1. **[[Root Port]] (RP)**
   - One per non-root switch
   - Best path to [[Root Bridge]] (lowest root path cost)
   - Always forwarding

2. **[[Designated Port]] (DP)**
   - One per LAN segment
   - Best path to root from segment's perspective
   - Always forwarding
   - All root bridge ports are DPs

3. **Non-Designated Port (Classic STP)**
   - Not root, not designated
   - **Blocking state** (prevents loops)
   - Replaced by [[Alternate Port]]/[[Backup Port]] in RSTP

**Port States (802.1D):**

| State | Function | Duration | Forwards Data? | Learns MACs? |
|-------|----------|----------|----------------|--------------|
| **Disabled** | Admin down | N/A | No | No |
| **Blocking** | Receives BPDUs only | 20s | No | No |
| **Listening** | Elects root, processes BPDUs | 15s | No | No |
| **Learning** | Learns [[MAC Address]]es | 15s | No | Yes |
| **Forwarding** | Full operation | Stable | Yes | Yes |

**Total convergence**: 50 seconds (20 + 15 + 15)

**[[Path Cost]]:**

Metric for selecting best path to root:

| Link Speed | 802.1D-2004 Cost |
|------------|------------------|
| 10 Mbps | 2,000,000 |
| 100 Mbps | 200,000 |
| 1 Gbps | 20,000 |
| 10 Gbps | 2,000 |
| 100 Gbps | 200 |

**Root Path Cost** = Sum of costs on path to root bridge

**[[BPDU]] (Bridge Protocol Data Unit):**

Messages exchanged between switches:
- **Configuration BPDU**: Contains Root Bridge ID, sender Bridge ID, root path cost, port ID, timers
- **[[TCN BPDU]] (Topology Change Notification)**: Sent upstream when port state changes (link down, new port forwarding, switch added/removed)
  - Triggers [[MAC Address Table (CAM)]] flush
  - Propagates to [[Root Bridge]] for network-wide notification
- **Frequency**: Every 2 seconds (Hello Time)
- **Destination MAC**: 01:80:C2:00:00:00

**STP Timers:**
- **Hello Time**: 2 seconds (BPDU interval)
- **Max Age**: 20 seconds (BPDU expiration)
- **Forward Delay**: 15 seconds (listening/learning state duration)

### 9.2.2 STP Calculation

**Step 1: Elect [[Root Bridge]]**
- Switch with lowest [[Bridge ID]] becomes root
- All switches participate; initially assume self is root
- Exchange BPDUs; lowest ID wins

**Step 2: Select [[Root Port]] on each non-root switch**

Selection criteria (priority order):
1. **Lowest root path cost**
2. **Lowest upstream Bridge ID** (sender)
3. **Lowest upstream port priority**
4. **Lowest upstream port ID** (number)

**Step 3: Select [[Designated Port]] on each segment**

One designated port per LAN segment (link):
1. **Lowest root path cost** from segment to root
2. **Lowest Bridge ID** on segment
3. **Lowest port priority**
4. **Lowest port ID**

Winner = Designated Port (forwarding); loser = Non-Designated (blocking)

**Step 4: Block non-designated ports**

Ports that are neither root nor designated → blocking state (prevents loops)

**Example Topology:**
```
SW1 (Priority 4096) --- SW2 (Priority 32768)
  |                        |
  +------- SW3 (Priority 32768)

1. SW1 becomes Root Bridge (lowest Bridge ID)
2. SW2's port to SW1 = Root Port (direct, lowest cost)
3. SW3's port to SW1 = Root Port (direct, lowest cost)
4. SW2-SW3 link:
   - SW2's port = Designated (lower Bridge ID than SW3)
   - SW3's port = Non-Designated/Blocking (higher Bridge ID)
```

### 9.2.3 Topology Change

**Fault Causes (What Triggers Topology Change):**
- Port down: Link failure (cable unplugged), admin shutdown, remote switch failure
- Port up: Link recovery (cable plugged), admin startup, remote switch recovery
- Port state transition: Blocking → Forwarding (port unblocked)
- [[Root Bridge]] change: Original root fails; new root elected
- [[Root Port]] change: Faster path discovered; original path blocked
- [[Designated Port]] change: Better path from segment to root
- Switch added: New switch joins topology
- Switch removed: Switch powered off or disconnected

**[[STP]] (802.1D) Process:**

1. **Detection**: Switch detects change (port transitions to/from forwarding)
2. **TCN Generation**: Switch sends **[[TCN BPDU]]** upstream via [[Root Port]]
3. **Propagation**: Each hop acknowledges with TCA; [[TCN BPDU]] relayed to root
4. **Root Handling**: [[Root Bridge]] receives [[TCN BPDU]]; sets TC flag in Configuration BPDUs
5. **Flood**: Root floods TC-flagged BPDUs to all switches
6. **MAC Flush**: All switches flush [[MAC Address Table (CAM)]]
7. **Accelerated Aging**: Forward Delay reduced from 300s → 15s for faster re-learning
8. **Duration**: 35 seconds (Max Age 20s + Forward Delay 15s)

**Impact:**
- Temporary unknown unicast flooding (MACs re-learned)
- Increased CPU load (BPDU processing, MAC flush)
- Potential packet loss during convergence
- **Total STP convergence**: 50+ seconds (with hop-by-hop propagation delays)

**[[RSTP]] Improvement:**
- [[TCN BPDU]] sent immediately on all designated/root ports (not just to root)
- Only affected switches flush MACs (not entire network)
- Faster, less disruptive convergence: <5 seconds

## 9.3 Basic STP Configurations

**Enable STP (usually enabled by default):**
```
[Huawei] stp enable

# Set STP mode
[Huawei] stp mode stp      ; Classic 802.1D
[Huawei] stp mode rstp     ; Rapid (recommended)
[Huawei] stp mode mstp     ; Multiple Spanning Tree
```

**Configure Root Bridge:**
```
# Primary root (lowest priority)
[SW-Core1] stp priority 0

# Secondary root (backup)
[SW-Core2] stp priority 4096
```

**Adjust Port Cost (influence path selection):**
```
[Huawei] interface GigabitEthernet0/0/1
[Huawei-GigabitEthernet0/0/1] stp cost 10000
```

**Configure [[Edge Port]] (PortFast):**
```
[Huawei] interface GigabitEthernet0/0/10
[Huawei-GigabitEthernet0/0/10] stp edged-port enable
```
- Immediate forwarding (0-second convergence)
- For host-facing ports only (PCs, servers, printers)

**Enable [[STP Protection Mechanisms]]:**
```
# Root Guard (prevent unauthorized root)
[Huawei-GigabitEthernet0/0/5] stp root-protection

# BPDU Guard (shut down if BPDU received on edge port)
[Huawei-GigabitEthernet0/0/10] stp bpdu-protection

# Loop Guard (prevent loops from unidirectional failure)
[Huawei-GigabitEthernet0/0/24] stp loop-protection
```

**Verification:**
```
# Global STP status
display stp

# Per-interface details
display stp interface GigabitEthernet0/0/1

# Brief overview
display stp brief
```

## 9.4 Improvements Made in RSTP

[[RSTP]] (Rapid Spanning Tree Protocol, IEEE 802.1w) addresses STP's slow convergence.

**Key Improvements:**

### 1. Rapid Convergence

- **[[Edge Port]]s**: Transition to forwarding immediately (0 seconds)
- **Point-to-point links**: Proposal/Agreement handshake; <5 seconds
- **[[Alternate Port]]s**: Pre-calculated backup; <1-second failover

**Convergence Comparison:**

| Event | STP | RSTP |
|-------|-----|------|
| Link failure | 50 seconds | <1 second |
| New link | 50 seconds | <5 seconds |
| Edge port | 50 seconds | Immediate |

### 2. New Port Roles

- **[[Root Port]]**: Best path to root (same as STP)
- **[[Designated Port]]**: Best for segment (same as STP)
- **[[Alternate Port]]**: Backup path to root (replaces non-designated)
- **[[Backup Port]]**: Backup for same segment (rare; hub scenario)

Alternate/backup distinction enables rapid failover.

### 3. Simplified Port States

| RSTP State | STP Equivalent | Function |
|------------|----------------|----------|
| **Discarding** | Blocking, Listening, Disabled | Not forwarding |
| **Learning** | Learning | Learning MACs |
| **Forwarding** | Forwarding | Full operation |

### 4. Proposal/Agreement Mechanism

When new link established between switches:

1. **Downstream switch** sends Proposal BPDU
2. **Upstream switch** (closer to root):
   - Syncs (blocks other non-edge ports if needed)
   - Sends Agreement BPDU
3. **Downstream port** transitions to forwarding immediately

No 30-second timer; rapid convergence.

### 5. BPDU Handling

- **Every switch generates BPDUs** (not just relayed from root)
- **3 missed BPDUs** (6 seconds) → declare neighbor failed
- Faster failure detection than STP (20-second Max Age)

### 6. Topology Change

- **Localized impact**: Only affected switches flush MACs
- **Edge ports excluded**: Don't trigger TCs; MACs not flushed
- **Less disruptive** than STP global flush

**Configuration:**
```
[Huawei] stp mode rstp

interface GigabitEthernet0/0/10
  stp edged-port enable   ; Immediate forwarding
```

## 9.5 STP Advancement

**[[MSTP]] (Multiple Spanning Tree Protocol, IEEE 802.1s):**

Enables **load balancing** by mapping [[VLAN]]s to multiple spanning tree instances.

**Problem with STP/RSTP:**
- Single spanning tree for all VLANs
- Blocked links unused for all VLANs (wasted bandwidth)

**MSTP Solution:**
- **Multiple instances**: Group VLANs into instances (MSTI 0-4094)
- **Per-instance topology**: Each instance has own spanning tree
- **Load balancing**: VLAN 10/20 use Link A; VLAN 30/40 use Link B

**Key Concepts:**

- **MST Region**: Group of switches with identical:
  - Region name
  - Revision number
  - VLAN-to-instance mapping
  
- **MSTI 0** (IST): Internal Spanning Tree; default; includes unmapped VLANs

- **CST** (Common Spanning Tree): Connects MST regions; treats each region as single bridge

**Configuration:**
```
# Enable MSTP
[Huawei] stp mode mstp

# Configure MST region
[Huawei] stp region-configuration
[Huawei-mst-region] region-name Campus-A
[Huawei-mst-region] revision-level 1
[Huawei-mst-region] instance 1 vlan 10 20
[Huawei-mst-region] instance 2 vlan 30 40
[Huawei-mst-region] active region-configuration

# Set root per instance
[Huawei] stp instance 1 priority 4096
[Huawei] stp instance 2 priority 8192
```

**Load Balancing Example:**
```
Topology: SW1 -- SW2 (two parallel links)

MSTI 1 (VLANs 10, 20):
  - SW1 root for MSTI 1
  - Link 1 active; Link 2 blocked

MSTI 2 (VLANs 30, 40):
  - SW2 root for MSTI 2
  - Link 2 active; Link 1 blocked

Result: Both links utilized; traffic distributed
```

**MSTP Benefits:**
- Bandwidth efficiency (utilizes redundant links)
- Scalable (single BPDU carries all instances)
- Backward compatible (interoperates with STP/RSTP at boundaries)

**Best Practices:**
- **Consistent region config**: All switches in region must match
- **2-4 instances**: Avoid over-complicating
- **Root placement**: Distribute roots across core for load balancing
- **Align with VLANs**: Group related VLANs into instances
