# Edge Port (PortFast)

An [[RSTP]]/[[MSTP]] port connected to end devices (not switches), transitioning to forwarding immediately without STP delays.

## Characteristics

- **Immediate forwarding**: Skips listening/learning states (0-second convergence)
- **No [[BPDU]]s expected**: Assumes no switches connected
- **Host-facing**: PCs, servers, printers, IP phones
- **Auto-reverts**: If BPDU received → becomes normal STP port

## Why Use Edge Ports?

### Without Edge Port
- Host boots, sends DHCP request
- Port in blocking state (20s) → listening (15s) → learning (15s)
- **50-second delay** before forwarding
- DHCP times out, host fails to obtain IP

### With Edge Port
- Port immediately forwards
- Host gets DHCP response instantly
- No boot delays

## Configuration (Huawei VRP)

```
# Per-port configuration
interface GigabitEthernet0/0/1
  stp edged-port enable

# Global configuration (rarely used)
stp edged-port default
```

## Safety: BPDU Protection

Prevents loops if switch mistakenly connected to edge port:

```
interface GigabitEthernet0/0/1
  stp edged-port enable
  stp bpdu-protection
```

**Behavior**: If [[BPDU]] received → port goes err-disabled (shut down).

## Edge Port vs. PortFast

- **Edge Port**: RSTP/MSTP term (IEEE 802.1w/s)
- **PortFast**: Cisco STP term (same function)
- Huawei uses "edged-port"

## Edge Port Behavior on Topology Change

- **Does not trigger [[Topology Change]]**: Link up/down on edge port doesn't send TC
- **MAC flush exception**: Edge port MACs not flushed during TC
- **Reduces disruption**: Fewer unnecessary TCs

## When NOT to Use

- **Switch-to-switch links**: Use normal STP (with proposal/agreement)
- **Trunk ports**: Could create loops
- **Uplinks**: Could cause bridging loops

## Verification

```
display stp interface GigabitEthernet0/0/1
  - Port Type: Edge Port
  - Status: Forwarding
  - Port Protocol: Enabled
```

## Related Concepts

- [[RSTP]]
- [[MSTP]]
- [[STP]]
- [[Topology Change]]
- [[BPDU]]
- BPDU Guard/Protection
