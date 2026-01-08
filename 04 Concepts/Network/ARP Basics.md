# ARP Basics

Address Resolution Protocol maps IPv4 addresses to [[MAC Address]]es within a [[Broadcast Domain]]. Often the first step before Ethernet frame forwarding.

## Process
1. Host sends ARP Request (broadcast) asking "Who has IP X?"
2. Owner replies with ARP Reply (unicast) containing its MAC
3. Sender caches IP→MAC mapping

## Interaction with Switching
- ARP Requests are broadcast; switches flood within VLAN
- ARP Replies are unicast; help populate switch [[MAC Address Table (CAM)]]
- Unknown unicast flooding may occur before ARP cache is learned

## Security/Performance
- ARP spoofing mitigations: DHCP snooping + DAI, static ARP
- Excessive ARP in large broadcast domains increases chatter

## Related Concepts
- [[Ethernet Frame]]
- [[Broadcast]]
- [[Ethernet Switch]]
