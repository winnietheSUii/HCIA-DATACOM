# Module 11: Eth-Trunk iStack and CSS

## 11.1 Network Reliability Requirements

Modern campus networks require high availability, redundancy, and scalable bandwidth. Key goals:
- Eliminate single points of failure (links/devices)
- Provide fast convergence on failures
- Expand bandwidth without changing topology
- Keep management simple across multiple devices

Common methods:
- [[Link Aggregation]] via [[Eth-Trunk]] for link redundancy and bandwidth
- Device virtualization: [[iStack]] and [[CSS]] for dual-active uplinks
- Baseline loop prevention: [[STP]]/[[RSTP]]/[[MSTP]]
- Fast detection: [[BFD]] (optional in advanced designs)

## 11.2 Principle and Configuration of Link Aggregation

### 11.2.1 Principle of Link Aggregation

[[Link Aggregation]] bundles multiple physical Ethernet links into **one logical link** (e.g., [[Eth-Trunk]]).

Benefits:
- Reliability: One member fails → others carry traffic
- Bandwidth: Aggregate throughput (N × member speed)
- Load balancing: [[Link Aggregation Hashing]] spreads flows across members
- Simplified topology: Trunk appears as single link to [[STP]]

Key elements:
- **Aggregator**: Logical interface (Eth-Trunk1)
- **Members**: Physical ports of same speed/duplex/VLAN config
- **Mode**: [[Manual Link Aggregation]] (static) or [[LACP]] (dynamic)

Load-balancing behavior:

**Per-Flow Load Balancing** (Default & Recommended):
- Uses **hash algorithm** based on frame/packet fields to select egress member link
- Hash inputs (configurable): src/dst MAC, src/dst IP, src/dst TCP/UDP port
- **Single flow always uses same link** → packet order preserved
- Different flows distribute across members → aggregate bandwidth utilized
- Example: Flow1 (PC1→Server1) always on Link1; Flow2 (PC2→Server2) on Link2
- **Best for**: Enterprise networks, TCP traffic, applications sensitive to reordering

**Per-Packet Load Balancing** (Not Recommended):
- Distributes packets **round-robin** across member links without considering flow
- **Packets within same flow can take different paths** → out-of-order delivery
- Causes **TCP performance degradation**: duplicate ACKs, retransmissions, reduced throughput
- **Only viable for**: Special cases like bonded links with identical delay, or UDP/non-ordered protocols
- **Disabled by default** on Huawei switches

**Hash algorithms** (per-flow):
- `src-dst-mac`: Layer 2 only; good for many hosts
- `src-dst-ip`: Layer 3; better distribution for routed traffic
- `src-dst-ip src-dst-port`: L3 + L4; **best entropy** for diverse flows

Configuration example:
```
[Switch-Eth-Trunk1] load-balance src-dst-ip src-dst-port
```

Why per-flow is critical:
- TCP requires **in-order delivery** for optimal performance
- Hash ensures **flow affinity** (same 5-tuple = same link)
- Trade-off: Single elephant flow limited by one member's bandwidth

Choose policy with higher entropy (include IP + ports) for better distribution. See [[Link Aggregation Hashing]].

### 11.2.2 Manual Mode

Static bundling without a control protocol.
- Both ends must be configured identically
- No LACP negotiation; simpler but less safe
- Risk of blackholes/loops if configs mismatch

Use when:
- Simple point-to-point links
- Protocols restricted

### 11.2.3 LACP Mode

[[LACP]] (IEEE 802.3ad/802.1AX) dynamically negotiates aggregation:
- Active/Passive roles exchange LACPDU frames
- Auto-detects mismatches; removes failed members
- Supports priorities and timers

Advantages over manual:
- Safer formation, better fault handling, easier operations

LACP details (must-know):
- **System ID/priority**: Selects which device forms aggregator when multiple candidates exist
- **Port priority**: Influences which members are active vs standby when max members reached
- **Actor/Partner states**: Activity (Active/Passive), Timeout (Short/Long), Synchronization, Collecting, Distributing
- **Timeout**: Short (fast) vs Long (default) periodic; fast detects failures quicker
- **Member consistency**: Speed/duplex/VLAN/trunk settings must match
- **Min-links**: Set minimum active members required to bring trunk up (protects against low-capacity operation)

Verification (verbose):
```
display lacp verbose
```

### 11.2.4 Typical Application Scenarios

- Access → Aggregation: Bundle uplinks for bandwidth/redundancy
- Aggregation ↔ Core: High-capacity interconnects
- Server NIC teaming: Multi-link server uplinks to switches
- Cross-device: With [[CSS]]/[[iStack]], form MC-LAG-like bundles across two core switches

### 11.2.5 Configuration Example

Create Eth-Trunk, add members, set mode and hashing.

Switch A:
```
[SwitchA] interface Eth-Trunk1
[SwitchA-Eth-Trunk1] mode lacp           ; or: mode manual
[SwitchA-Eth-Trunk1] load-balance src-dst-ip  ; choose hashing policy
[SwitchA] interface GigabitEthernet0/0/1
[SwitchA-GE0/0/1] eth-trunk 1
[SwitchA] interface GigabitEthernet0/0/2
[SwitchA-GE0/0/2] eth-trunk 1
```

Switch B (matching trunk):
```
[SwitchB] interface Eth-Trunk1
[SwitchB-Eth-Trunk1] mode lacp
[SwitchB-Eth-Trunk1] load-balance src-dst-ip
[SwitchB] interface GigabitEthernet0/0/3
[SwitchB-GE0/0/3] eth-trunk 1
[SwitchB] interface GigabitEthernet0/0/4
[SwitchB-GE0/0/4] eth-trunk 1
```

Verification:
```
display eth-trunk
  - State, members, mode (manual/lacp), min-links
  - Load-balance policy (src/dst MAC/IP/L4)

display lacp
  - Actor/partner system IDs, priorities
  - Port priorities, timeout (short/long), sync state
```

Troubleshooting (must-know):
- **Members stay Unselected**: Mismatched VLAN/trunk or speed/duplex → align configs on both ends
- **Asymmetric hashing**: Few large flows on one link → include L4 ports in hashing; add members
- **Trunk flaps on failure**: Configure min-links to keep trunk down when capacity too low
- **Out-of-order packets**: Avoid per-packet load balancing; use per-flow hashing

## 11.3 Overview of iStack and CSS

### iStack

[[iStack]] stacks multiple switches into a single logical device:
- One management plane (single IP/CLI)
- Master/standby roles with fast failover
- Cross-stack [[Eth-Trunk]] supported (bundle links across members)

Verification:
```
display stack
```

### CSS

[[CSS]] (Cluster Switch System) virtualizes two core switches into one system:
- Enables **multi-chassis link aggregation** (active-active uplinks)
- Increases reliability (no single uplink failure)
- Uses [[Dual-Active Detection]] to prevent split-brain

Verification:
```
display css
```

### iStack vs CSS (Overview)
- Both: Single logical system, simplified management, faster convergence
- iStack: General stacking across multiple members
- CSS: Core-focused clustering enabling MC-LAG-like active-active uplinks
