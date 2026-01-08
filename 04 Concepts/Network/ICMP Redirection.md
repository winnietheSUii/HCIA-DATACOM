# ICMP Redirection

A mechanism where a router informs a host that it should send packets for certain destinations to a different router.

## Purpose
- **Route optimization** - Improve packet forwarding paths
- **Gateway selection** - Help hosts choose the best next-hop router
- **Reduces unnecessary router hops** - Directs traffic more efficiently

## How It Works
1. Host sends packet to Router A
2. Router A recognizes a better path via Router B
3. Router A sends ICMP Redirect message to host
4. Host updates its routing table to use Router B for that destination

## When Used
- When a host initially sends packets to a suboptimal gateway
- Router detects a more direct path available
- Helps hosts on the same network segment choose better routes

## Security Note
- Modern systems often ignore redirects due to security concerns
- Can be exploited for [[Man-in-the-Middle]] attacks