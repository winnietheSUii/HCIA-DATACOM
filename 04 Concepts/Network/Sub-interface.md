	# Sub-interface

Layer 3 interface on a router connected via a **single physical port** using [[VLAN]] tagging (802.1Q). Each [[Sub-interface]] represents one [[VLAN]]; enables **Router on a Stick** topology.
		
## Purpose

Allow **one physical router port** to handle **multiple [[VLAN]]s** by tagging traffic with VLAN IDs. Each [[Sub-interface]] terminates one [[VLAN]] and acts as the gateway for that VLAN's hosts.

## Configuration

**Syntax** (Huawei VRP):
```
[Router-GigabitEthernet0/0/0] ip address 192.168.10.1 255.255.255.0
[Router-GigabitEthernet0/0/0] dot1q termination vid 10
[Router-GigabitEthernet0/0/0] arp broadcast enable
[Router-GigabitEthernet0/0/0] quit

[Router] interface GigabitEthernet0/0/0.20
[Router-GigabitEthernet0/0/0.20] ip address 192.168.20.1 255.255.255.0
[Router-GigabitEthernet0/0/0.20] dot1q termination vid 20
[Router-GigabitEthernet0/0/0.20] arp broadcast enable
```

**Naming**: 
- Physical: `GigabitEthernet0/0/0`
- [[Sub-interface]]: `GigabitEthernet0/0/0.10` (port.VLAN ID)

**Key Commands**:
- `dot1q termination vid 10`: Bind [[Sub-interface]] to VLAN ID 10 (802.1Q tag)
- `arp broadcast enable`: Allow ARP broadcasts on this [[VLAN]]
- `ip address`: Gateway IP for this [[VLAN]]

## Switch Configuration (for Sub-interface)

**[[Trunk Port]]** toward router (carries multiple VLANs):
```
[Switch-GigabitEthernet0/0/1] port link-type trunk
[Switch-GigabitEthernet0/0/1] port trunk allow-pass vlan 10 20 30
```

Router receives **tagged frames** on single port; [[Sub-interface]]s tag/untag on egress.

## Topology Example

```
                    ┌─────────────────┐
                    │     Router      │
                    │  G0/0/0.10      │
                    │  192.168.10.1   │
                    │  VLAN 10        │
                    │                 │
                    │  G0/0/0.20      │ (Single physical port)
                    │  192.168.20.1   │ (Multiple Sub-interfaces)
                    │  VLAN 20        │
                    └────────┬────────┘
                             │ (Trunk: tagged frames)
                             │ 802.1Q: 10, 20
                    ┌────────┴────────┐
                    │     Switch      │
                    │  (VLAN-aware)   │
                    └────────┬────────┘
                 ┌───────────┼───────────┐
            (Access)    (Access)    (Trunk)
            VLAN 10     VLAN 20      VLAN 10, 20
                │           │           │
              PC1         PC2        Router
           V10 Host     V20 Host      G0/0/0
```

**Communication Flow** (PC1 VLAN 10 → PC2 VLAN 20):
1. PC1 sends to PC2 (dest IP 192.168.20.20)
2. ARP: Resolves default gateway = Router VLAN 10 IP (192.168.10.1)
3. PC1 → Switch (untagged frame, access port, VLAN 10 assumed)
4. Switch → Router (tagged frame, trunk, VLAN ID 10 tag)
5. Router G0/0/0.10 receives; checks routing table
6. Router G0/0/0.20 egress; **retagged** as VLAN 20
7. Router → Switch (tagged frame, VLAN 20 tag)
8. Switch → PC2 (untagged frame, access port, VLAN 20 removed)
9. PC2 receives (source MAC = Router's VLAN 20 interface MAC)

## Advantages

- **Single router port**: Reduces router expense (useful for legacy routers with few ports)
- **Multiple VLANs**: One physical link handles all [[VLAN]] routing
- **Simple setup**: Easy to understand; VLAN ID = [[Sub-interface]] number

## Disadvantages

- **Bandwidth limitation**: Single physical link (e.g., 1 Gbps) shared across all [[VLAN]]s
  - If VLAN 10 + VLAN 20 both heavily used, bandwidth contention
  - Single link failure = all inter-VLAN routing down
- **Latency**: Router CPU processes every frame (older hardware slow)
- **Not scalable**: Dozens of [[Sub-interface]]s on one port inefficient
- **Legacy**: Replaced by modern [[VLANIF Interface]] approach

## Comparison: Sub-interface vs. [[VLANIF Interface]]

| Feature | [[Sub-interface]] | [[VLANIF Interface]] |
|---------|------|------|
| **Binding** | Tied to physical port | Virtual; independent of port |
| **Bandwidth** | Single physical link | Multiple paths possible |
| **Scalability** | Limited (port saturation) | Scales better |
| **Legacy** | Traditional "Router on a Stick" | Modern approach |
| **Config** | `dot1q termination vid X` | `vlan X` then `vlanif X` |
| **Use case** | Small/legacy networks | Enterprise/modern networks |

## When to Use

- **Small office**: Few VLANs; limited router ports
- **Testing**: Lab environment; verify inter-VLAN routing
- **Legacy equipment**: Old routers without VLANIF support
- **Not recommended**: Production enterprise (use [[VLANIF Interface]] or multi-port router)

## Related Concepts

- [[VLAN]]
- [[VLANIF Interface]]
- [[Trunk Port]]
- [[802.1Q]]
- [[Inter-VLAN Communication]]
- [[Routing]]
