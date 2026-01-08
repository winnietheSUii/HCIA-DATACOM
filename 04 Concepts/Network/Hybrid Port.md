# Hybrid Port

A Huawei-specific port type that allows granular control over VLAN tagging on a per-VLAN basis.

## Characteristics

- **Flexible tagging**: Each VLAN can be tagged or untagged independently
- **Multiple untagged VLANs**: Unlike trunk (one native), hybrid can send multiple VLANs untagged
- **Use cases**: Complex VLAN scenarios, voice+data on same port, legacy interop

## Configuration (Huawei VRP)

```
interface GigabitEthernet0/0/5
  port link-type hybrid
  port hybrid pvid vlan 10           ; default VLAN for untagged ingress
  port hybrid untagged vlan 10 20    ; send VLANs 10, 20 untagged
  port hybrid tagged vlan 30 40      ; send VLANs 30, 40 tagged
```

## PVID (Port VLAN ID)

- Default VLAN for incoming untagged frames
- Similar to access port's default VLAN or trunk's native VLAN

## Behavior

- **Ingress**:
  - Untagged → assigned to PVID
  - Tagged → uses tag's VID
- **Egress**: Per-VLAN config determines if frame is tagged or untagged

## Comparison

| Feature | Access | Trunk | Hybrid |
|---------|--------|-------|--------|
| VLANs | 1 | Multiple | Multiple |
| Untagged egress | 1 VLAN | Native VLAN | Per-VLAN config |
| Tagged egress | None | All except native | Per-VLAN config |
| Flexibility | Low | Medium | High |

## When to Use

- Voice + data VLANs both untagged on same port
- Complex multi-vendor interop
- Legacy devices requiring specific tagging behavior

## Vendor Support

- **Huawei**: Native support
- **Cisco**: No direct equivalent; use voice VLAN or multiple SVIs
- **HP/Aruba**: Similar concepts (tagged/untagged member)

## Related Concepts

- [[VLAN]]
- [[802.1Q]]
- [[Access Port]]
- [[Trunk Port]]
