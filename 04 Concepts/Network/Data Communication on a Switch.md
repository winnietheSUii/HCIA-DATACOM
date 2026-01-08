# Data Communication on a Switch

End-to-end process for frame delivery inside a switched LAN/VLAN.

## Steps
1. Host builds Ethernet frame (Dst/Src [[MAC Address]], EtherType, payload, FCS)
2. Frame sent to switch port (ingress)
3. Switch **learns** source MAC → updates [[MAC Address Table (CAM)]]
4. Switch decides based on destination MAC:
   - Known unicast: forward to specific port
   - Unknown unicast: flood within VLAN
   - Broadcast: flood within VLAN
   - Multicast: flood (or prune with IGMP snooping)
5. Egress port transmits frame to destination host
6. Aging timer removes stale MAC entries if no traffic seen

## Collision and Broadcast Domains
- Each switch port = separate [[Collision Domain]]
- Entire VLAN = one [[Broadcast Domain]] unless routed

## Related Concepts
- [[Ethernet Switch]]
- [[MAC Address Table (CAM)]]
- [[Broadcast Domain]]
- [[Collision Domain]]
