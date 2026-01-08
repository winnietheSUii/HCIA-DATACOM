# MAC-Based VLAN

Dynamic [[VLAN]] assignment based on source [[MAC Address]], allowing users to maintain VLAN membership regardless of physical port.

## How It Works

1. Administrator configures MAC-to-VLAN mappings
2. When frame arrives, switch checks source MAC
3. Frame assigned to VLAN based on MAC lookup
4. User can move to different port; VLAN follows

## Configuration (Huawei VRP Example)

```
vlan 10
  mac-vlan mac-address 00:11:22:33:44:55

interface GigabitEthernet0/0/1
  mac-vlan enable
```

## Advantages

- **User mobility**: VLAN follows user, not port
- **Flexibility**: No reconfiguration when users move
- **Centralized management**: MAC-to-VLAN mapping in one place

## Disadvantages

- **Scalability**: Large MAC databases difficult to maintain
- **Security**: MAC spoofing risk (mitigate with port security)
- **Complexity**: More difficult to troubleshoot
- **Performance**: Lookup overhead (usually negligible on modern hardware)

## Use Cases

- Hot-desking environments
- Guest/contractor access with pre-registered devices
- Universities, conference centers

## vs. [[Port-Based VLAN]]

| Feature | Port-Based | MAC-Based |
|---------|------------|-----------|
| Assignment | Static per port | Dynamic per MAC |
| Mobility | Low | High |
| Configuration | Simple | Complex |
| Security | Better (physical control) | Requires port security |

## Related Concepts

- [[VLAN]]
- [[MAC Address]]
- [[Port-Based VLAN]]
- [[Protocol-Based VLAN]]
- 802.1X (authentication-based VLAN)
