# Access Port

A switch port configured to belong to a single [[VLAN]], sending and receiving untagged frames.

## Characteristics

- **Single VLAN membership**: All traffic belongs to one VLAN
- **Untagged frames**: End hosts send/receive standard Ethernet frames
- **Switch handles tagging**: Adds [[802.1Q]] tag internally on ingress; removes on egress
- **Typical use**: Connect end devices (PCs, printers, servers, IP phones*)

## Configuration (Huawei VRP)

```
interface GigabitEthernet0/0/1
  port link-type access
  port default vlan 10
```

## Behavior

- Ingress: Frame arrives untagged → switch tags with port's VLAN ID
- Forwarding: Frame processed using [[MAC Address Table (CAM)]] + VLAN
- Egress: If destined for same VLAN access port → tag removed → untagged frame sent

## vs. [[Trunk Port]]

| Feature | Access Port | Trunk Port |
|---------|-------------|------------|
| VLANs | One | Multiple |
| Tagging | Untagged | Tagged ([[802.1Q]]) |
| Use | End hosts | Switch-to-switch links |

## Special Cases

- **Voice VLAN**: Some switches support dual VLAN on access port (data + voice)
- **Dynamic VLAN**: Assignment based on MAC/802.1X (still functions as access port)

## Related Concepts

- [[VLAN]]
- [[802.1Q]]
- [[Trunk Port]]
- [[Hybrid Port]]
- [[Port-Based VLAN]]
