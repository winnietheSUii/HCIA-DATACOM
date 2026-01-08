# Address Learning

Mechanism by which an [[Ethernet Switch]] builds its [[MAC Address Table (CAM)]].

## Process
1. Frame arrives at ingress port
2. Switch records source [[MAC Address]] with ingress port/VLAN
3. Timer starts (aging)
4. Entry refreshed on new frames from same MAC/port

## Importance
- Enables unicast forwarding instead of flooding
- Allows host mobility (updates when MAC seen on new port)

## Related Concepts
- [[Forwarding Behavior (Switch)]]
- [[Aging Timer]]
- [[Unknown Unicast Flooding]]
