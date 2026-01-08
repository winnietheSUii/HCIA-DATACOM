# Designated Port

A port on a LAN segment with the best path to the [[Root Bridge]] from that segment's perspective in [[STP]]/[[RSTP]].

## Characteristics

- **One per LAN segment**: Each physical link/segment has exactly one DP
- **Always forwarding**: Sends/receives data and [[BPDU]]s
- **Sends BPDUs**: Advertises root bridge info and path cost
- **All root bridge ports**: All ports on [[Root Bridge]] are designated ports

## Selection Criteria (Priority Order)

When multiple switches connect to same segment:

1. **Lowest root path cost** to root bridge
2. **Lowest sender Bridge ID**
3. **Lowest sender port priority**
4. **Lowest sender port ID**

Winner becomes designated port; loser becomes non-designated (blocking) or [[Alternate Port]] (RSTP).

## Designated Port Role

- **Forwards BPDUs** downstream (away from root)
- **Learns MAC addresses** ([[MAC Address Table (CAM)]])
- **Forwards data frames**

## On [[Root Bridge]]

- All ports are designated ports
- Root bridge is authoritative source of BPDUs
- Root doesn't have [[Root Port]]

## On Non-Root Switches

- Ports that are "best for segment" become designated
- Typically, downstream-facing ports toward edge
- Upstream-facing port becomes [[Root Port]]

## Example Topology

```
Root Bridge
  |  All ports = Designated
  |
  +--- SW1 ---+--- SW2
       ↓           ↓
   DP (to edge)  DP (to edge)
   RP (to root)  RP (to root)
```

SW1 ↔ SW2 link:
- One port = Designated (lower Bridge ID)
- Other port = Alternate/Blocking (higher Bridge ID)

## Configuration (Indirectly)

Designated port selection is automatic, but can be influenced:

```
# Lower bridge priority → more likely to have DPs
[Huawei] stp priority 8192

# Lower port cost → more likely to be DP on segment
interface GigabitEthernet0/0/1
  stp cost 10000
```

## Verification

```
display stp interface GigabitEthernet0/0/1
  - Role: Designated Port
  - State: Forwarding
  - Designated Bridge: <Bridge ID>
```

## Related Concepts

- [[STP]]
- [[RSTP]]
- [[Root Bridge]]
- [[Root Port]]
- [[Alternate Port]]
- [[BPDU]]
