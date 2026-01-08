# MSTP (Multiple Spanning Tree Protocol)

IEEE 802.1s protocol that maps multiple [[VLAN]]s to spanning tree instances, improving efficiency and load balancing.

## The Problem with STP/RSTP

- **Single spanning tree**: All VLANs use same topology
- **Wasted bandwidth**: Blocked links unused for all VLANs
- **No load balancing**: Cannot distribute traffic across redundant links

## MSTP Solution

- **Multiple instances**: Group VLANs into instances (MSTIs)
- **Per-instance topology**: Each instance has own spanning tree
- **Load balancing**: Different VLANs use different links
- **Efficiency**: Better bandwidth utilization

## Key Concepts

### MST Region
- Group of switches with identical:
  - **Region name**
  - **Revision number**
  - **VLAN-to-instance mapping**
- Switches in same region share same MSTP config
- Regions connect via CST (Common Spanning Tree)

### MST Instance (MSTI)
- Spanning tree instance for subset of VLANs
- **MSTI 0**: IST (Internal Spanning Tree); default, includes all unmapped VLANs
- **MSTI 1-4094**: User-defined instances

### CST (Common Spanning Tree)
- Connects MST regions
- Treats each region as single virtual bridge

### IST (Internal Spanning Tree)
- MSTI 0; mandatory
- Exchanges BPDUs with other regions
- Represents region to external switches

## Configuration (Huawei VRP)

```
# Enable MSTP
stp mode mstp

# Configure MST region
stp region-configuration
  region-name Company-A
  revision-level 1
  instance 1 vlan 10 20       ; VLANs 10, 20 → MSTI 1
  instance 2 vlan 30 40       ; VLANs 30, 40 → MSTI 2
  active region-configuration

# Set root bridge per instance
stp instance 1 priority 4096   ; Root for MSTI 1
stp instance 2 priority 8192   ; Root for MSTI 2

# Set port cost per instance
interface GigabitEthernet0/0/1
  stp instance 1 cost 10000
  stp instance 2 cost 20000
```

## Load Balancing Example

```
Topology:
  SW1 --- SW2
   |   X   |
  SW3 --- SW4

MSTI 1 (VLANs 10, 20):
  - SW1 is root
  - Link SW1-SW2 active
  - Link SW3-SW4 blocked

MSTI 2 (VLANs 30, 40):
  - SW3 is root
  - Link SW3-SW4 active
  - Link SW1-SW2 blocked

Result: Both links utilized; traffic distributed
```

## BPDU Handling

- **MST BPDU**: Single BPDU carries info for all instances (vs. PVST+ = one BPDU per VLAN)
- **Efficient**: Reduces BPDU overhead in large VLAN environments
- **Backward compatible**: MSTP can interoperate with STP/RSTP (at region boundary)

## Convergence

- **Within region**: Rapid convergence like [[RSTP]]
- **Between regions**: Depends on CST convergence

## Best Practices

- **Consistent region config**: All switches in region must match name, revision, VLAN mapping
- **Small number of instances**: 2-4 instances typical (avoid over-complicating)
- **Align with VLANs**: Group related VLANs into instances
- **Root placement**: Distribute roots across core switches for load balancing

## Verification

```
display stp
display stp instance 1
display stp region-configuration
```

## vs. Other STP Variants

| Protocol | Instances | Load Balancing | BPDUs per VLAN | Standard |
|----------|-----------|----------------|----------------|----------|
| STP | 1 | No | 1 (for all) | 802.1D |
| RSTP | 1 | No | 1 (for all) | 802.1w |
| **MSTP** | Multiple | Yes | 1 (carries all) | 802.1s |
| PVST+ | Per-VLAN | Yes | 1 per VLAN | Cisco |

## Related Concepts

- [[STP]]
- [[RSTP]]
- [[VLAN]]
- [[Root Bridge]]
- [[Root Port]]
- [[Designated Port]]
- Load balancing
