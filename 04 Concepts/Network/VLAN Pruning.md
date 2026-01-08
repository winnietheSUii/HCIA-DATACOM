# VLAN Pruning

Process of restricting which [[VLAN]]s are allowed on a [[Trunk Port]] to reduce unnecessary traffic and improve security.

## Purpose

- **Reduce broadcast traffic**: Fewer VLANs = fewer broadcasts flooded on trunk
- **Security**: Limit VLAN exposure to only necessary switches
- **STP efficiency**: Smaller topology = faster convergence
- **Bandwidth**: Less wasted bandwidth on unused VLANs

## Default Behavior

- Most switches allow all VLANs (1-4094) on trunks by default
- Even if VLAN doesn't have active ports, broadcast/multicast still floods

## Configuration (Huawei VRP)

```
interface GigabitEthernet0/0/24
  port link-type trunk
  port trunk allow-pass vlan 10 20 30  ; Only VLANs 10, 20, 30 allowed
```

## Best Practices

- **Whitelist approach**: Only allow necessary VLANs
- **Prune VLAN 1**: Don't carry VLAN 1 on trunks (security)
- **Unused VLANs**: Remove from trunk configurations
- **Consistent pruning**: Same allowed VLANs on both ends of trunk
- **Management VLAN**: Create dedicated management VLAN, prune from most trunks

## Example Scenario

```
Switch A --- Trunk --- Switch B

Switch A has: VLAN 10 (Sales), VLAN 20 (Engineering), VLAN 30 (Guest)
Switch B has: VLAN 10 (Sales), VLAN 40 (HR)

Optimal trunk config: Allow only VLAN 10
  - No need for VLAN 20, 30, 40 on trunk
  - Reduces broadcast domain size
```

## VTP Pruning (Cisco)

- Automatic VLAN pruning using VLAN Trunking Protocol
- Dynamically prunes inactive VLANs from trunks
- Huawei: Manual pruning (no VTP)

## Related Concepts

- [[Trunk Port]]
- [[VLAN]]
- [[Broadcast Domain]]
- [[Flooding]]
