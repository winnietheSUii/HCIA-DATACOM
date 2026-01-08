# Native VLAN

The [[VLAN]] on a [[Trunk Port]] whose frames are sent and received **untagged**.

## Purpose

- Backward compatibility with non-VLAN-aware devices
- Management traffic often uses native VLAN
- Default: VLAN 1 (on most switches)

## Behavior

- **Ingress on trunk**: Untagged frame → assigned to native VLAN
- **Egress on trunk**: Native VLAN frames → sent untagged

## Configuration (Huawei VRP)

```
interface GigabitEthernet0/0/24
  port link-type trunk
  port trunk allow-pass vlan 10 20 30
  port trunk pvid vlan 1  ; native VLAN = 1 (default)
```

## Security Risks

### VLAN Hopping Attack

1. Attacker sends double-tagged frame (outer = native VLAN, inner = target VLAN)
2. First switch strips outer tag (native VLAN)
3. Inner tag remains → frame forwarded to target VLAN

**Mitigation**:
- Use non-default native VLAN (not VLAN 1)
- Disable native VLAN traffic: tag all VLANs on trunk
- Prune unused VLANs from trunks

### Native VLAN Mismatch

- Two switches with different native VLANs on trunk
- Untagged traffic on one switch ends up in wrong VLAN on other switch
- Can leak sensitive traffic

**Detection/Mitigation**:
- Enable CDP/LLDP native VLAN mismatch alerts
- Standardize native VLAN across all trunks
- Use uncommon VLAN ID (e.g., VLAN 999)

## Best Practices

- **Change from VLAN 1**: Use uncommon VLAN (e.g., 999)
- **Consistency**: Same native VLAN on both ends of trunk
- **Prune unused VLANs**: Reduce attack surface
- **Tag all VLANs**: Use `vlan dot1q tag native` (Cisco) or equivalent
- **Disable DTP**: Prevent auto-negotiation attacks

## Related Concepts

- [[Trunk Port]]
- [[VLAN]]
- [[802.1Q]]
- [[VLAN Tagging]]
