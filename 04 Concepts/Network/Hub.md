# Hub

A layer 1 repeater that broadcasts incoming bits to all ports. Creates a single shared [[Collision Domain]] and does not understand [[MAC Address]]es.

## Characteristics
- Operates at Physical layer (OSI L1)
- No forwarding decisions; repeats signal to all ports
- Half-duplex only; relies on [[CSMA-CD]] for access control
- One big collision domain; also one broadcast domain

## Limitations
- Low efficiency due to collisions
- No MAC learning or filtering
- Obsolete; replaced by [[Ethernet Switch]]es

## Related Concepts
- [[Ethernet]]
- [[Collision Domain]]
- [[Broadcast Domain]]
