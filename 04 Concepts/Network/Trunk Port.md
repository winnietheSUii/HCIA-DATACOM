# Trunk Port

A switch port that carries traffic for multiple [[VLAN]]s using [[802.1Q]] tagging.

## Characteristics

- **Multiple VLANs**: Transports frames for many VLANs simultaneously
- **Tagged frames**: [[802.1Q]] tag indicates VLAN ID
- **Native VLAN**: One VLAN sent untagged (default VLAN 1)
- **Typical use**: Switch-to-switch, switch-to-router links

## Configuration (Huawei VRP)

```
interface GigabitEthernet0/0/24
  port link-type trunk
  port trunk allow-pass vlan 10 20 30
  port trunk pvid vlan 1  ; native VLAN
```

## Allowed VLANs

- Trunk can be restricted to specific VLANs
- Default: Usually allows all VLANs (or VLAN 1 only, vendor-specific)
- Best practice: Prune unused VLANs to reduce broadcast/STP overhead

## Native VLAN

- Untagged traffic on trunk treated as native VLAN
- **Security risk**: Mismatched native VLANs between switches → traffic leaks
- Best practice: Use uncommon VLAN ID, disable native VLAN traffic if possible

## Behavior

- **Ingress**:
  - Tagged frame → VLAN ID from tag
  - Untagged frame → assigned to native VLAN
- **Egress**:
  - Native VLAN → sent untagged
  - Other VLANs → sent tagged

## DTP (Dynamic Trunking Protocol)

- Cisco proprietary; auto-negotiates trunk/access mode
- Huawei: Manual trunk configuration (no DTP)
- Security risk: DTP should be disabled on unused/edge ports

## vs. [[Access Port]]

| Feature | Trunk Port | Access Port |
|---------|------------|-------------|
| VLANs | Multiple | One |
| Tagging | Tagged (native untagged) | Untagged |
| Use | Inter-switch | End hosts |

## Related Concepts

- [[VLAN]]
- [[802.1Q]]
- [[Access Port]]
- [[Hybrid Port]]
- [[Inter-VLAN Routing]]
