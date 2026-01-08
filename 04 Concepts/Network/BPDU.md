# BPDU (Bridge Protocol Data Unit)

Layer 2 frames exchanged between switches running [[STP]]/[[RSTP]]/[[MSTP]] to elect [[Root Bridge]], calculate paths, and detect topology changes.

## Types

### Configuration BPDU
- **Purpose**: Root election, path cost advertisement, topology maintenance
- **Sent by**: Root bridge originates; other switches relay
- **Frequency**: Every 2 seconds (Hello Time)
- **Contains**:
  - Root Bridge ID
  - Sender's Bridge ID
  - Root path cost
  - Port ID
  - Timers (Hello, Max Age, Forward Delay)
  - Flags (Topology Change, Topology Change Acknowledgment)

### TCN BPDU (Topology Change Notification)
- **Purpose**: Signal topology change to root; triggers [[MAC Address Table (CAM)]] flush
- **Sent by**: Switch detecting topology change
- **Direction**: Upstream toward root via [[Root Port]]
- **Frequency**: Every 1 second until acknowledged
- **Acknowledgment**: Upstream switch responds with TCA flag in Configuration BPDU

**Fault Causes (Topology Change Triggers):**
- **Port transitioned to forwarding**: Port moved from blocking/learning to forwarding (link came up)
- **Port transitioned from forwarding**: Port moved from forwarding to blocking/listening (link failed)
- **Port disabled/enabled**: Administrative shutdown or startup
- **Link failure**: Physical link broken; port goes down
- **Link recovery**: Physical link restored; port comes back up
- **Switch added**: New switch joins STP domain
- **Switch removed**: Switch disconnected or failed
- **Root bridge failed**: [[Root Bridge]] becomes unreachable; new root elected
- **Port role change**: Root port → non-designated; designated → blocked (topology reshape)

### RSTP BPDU
- Enhanced version with additional flags:
  - Port role (root, designated, alternate, backup)
  - Learning/Forwarding state
  - Agreement/Proposal bits (rapid convergence)

## BPDU Frame Format

- **Destination MAC**: 01:80:C2:00:00:00 (Bridge Group Address)
- **EtherType**: 0x0026 (802.3 length field)
- **LLC/SNAP Header**: Identifies STP
- **BPDU payload**: 35 bytes (Configuration BPDU)

## BPDU Processing

### Superior BPDU
- Lower root Bridge ID, or
- Same root but lower root path cost, or
- Same cost but lower sender Bridge ID

**Action**: Switch updates [[Root Port]], recalculates topology

### Inferior BPDU
- Higher root Bridge ID, or worse path cost

**Action**: Ignored; switch sends own superior BPDU

### Equal BPDU
**Action**: Tie-breaking using sender port ID

## BPDU Timers

- **Hello Time**: 2 seconds (default)
- **Max Age**: 20 seconds (BPDU expiration; missing BPDUs trigger reconvergence)
- **Forward Delay**: 15 seconds (listening/learning duration in 802.1D)

## BPDU Guard

Security feature: disables port if BPDU received (prevents rogue switches).

```
interface GigabitEthernet0/0/1
  stp bpdu-protection
```

If BPDU received on edge port → port goes to err-disabled/down.

## BPDU Filter

Prevents BPDU transmission/reception on port:

```
interface GigabitEthernet0/0/1
  stp bpdu-filter enable
```

**Use case**: Rare; typically for connecting non-STP devices (risky).

## Verification

```
display stp interface GigabitEthernet0/0/1
  - Shows BPDUs sent/received
  - Root Bridge ID from received BPDUs
  - Timers
```

## Related Concepts

- [[STP]]
- [[RSTP]]
- [[Root Bridge]]
- [[Root Port]]
- [[Designated Port]]
- [[Topology Change]]
