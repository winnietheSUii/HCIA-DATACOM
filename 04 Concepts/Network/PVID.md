# PVID (Port VLAN ID)

The default [[VLAN]] assigned to untagged frames received on a switch port.

## Purpose

- **Ingress handling**: Determine VLAN for untagged frames
- **Hybrid/Trunk ports**: PVID = [[Native VLAN]]
- **Access ports**: PVID = port's VLAN

## Configuration (Huawei VRP)

```
# Access port (PVID implicit from default vlan)
interface GigabitEthernet0/0/1
  port link-type access
  port default vlan 10     ; PVID = 10

# Trunk port (PVID = native VLAN)
interface GigabitEthernet0/0/24
  port link-type trunk
  port trunk pvid vlan 1   ; PVID = 1 (default native)

# Hybrid port (explicit PVID)
interface GigabitEthernet0/0/5
  port link-type hybrid
  port hybrid pvid vlan 10
```

## Behavior by Port Type

### [[Access Port]]
- PVID = assigned VLAN (e.g., `port default vlan 10`)
- All ingress traffic → VLAN 10
- Egress: Tag removed

### [[Trunk Port]]
- PVID = native VLAN (default VLAN 1)
- Untagged ingress → assigned to PVID
- Tagged ingress → uses tag's VID
- Egress: PVID frames sent untagged; others tagged

### [[Hybrid Port]]
- PVID configured explicitly (`port hybrid pvid vlan X`)
- Ingress: Untagged → PVID; tagged → tag's VID
- Egress: Per-VLAN config (tagged or untagged)

## Common Issues

- **PVID mismatch**: Different PVIDs on trunk ends → traffic leaks
- **Native VLAN mismatch**: Same as PVID mismatch on trunks
- **Verification**: Check PVID on both ends of trunk

## Related Concepts

- [[VLAN]]
- [[Native VLAN]]
- [[Trunk Port]]
- [[Access Port]]
- [[Hybrid Port]]
