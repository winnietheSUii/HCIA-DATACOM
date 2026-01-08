# Multicast

Traffic sent from one sender to a group of receivers. In Ethernet, destination MAC has I/G bit = 1 but is not all-ones (broadcast).

## Behavior
- Switch floods multicast by default within VLAN
- With IGMP snooping, switch prunes forwarding to interested ports

## Examples
- IPv4 multicast MAC range: 01:00:5E:00:00:00 to 01:00:5E:7F:FF:FF

## Related Concepts
- [[Unicast]]
- [[Broadcast]]
- [[Ethernet Frame]]
- [[Ethernet Switch]]
