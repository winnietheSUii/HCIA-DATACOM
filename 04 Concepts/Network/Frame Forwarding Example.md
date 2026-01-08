# Frame Forwarding Example

End-to-end example of host-to-host communication in a switched Ethernet VLAN.

```
Host A (MAC AA-AA-AA-AA-AA-AA, IP 192.168.1.10)
Host B (MAC BB-BB-BB-BB-BB-BB, IP 192.168.1.20)
Same VLAN on Switch S1
```

## Steps
1. Host A ARPs for 192.168.1.20 (broadcast). Switch floods.
2. Host B replies with ARP Reply (unicast). Switch learns B's MAC on its port.
3. Host A sends data frame to B's MAC.
4. Switch has entry for B → forwards only to B's port (known unicast).
5. If B silent for aging interval, entry removed; next frame floods again.

## Key Behaviors
- Unknown unicast flooding until MAC learned
- Broadcast domain limited to VLAN
- Each switch port is separate [[Collision Domain]]

## Related Concepts
- [[ARP Basics]]
- [[MAC Address Table (CAM)]]
- [[Data Communication on a Switch]]
