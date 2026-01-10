# NAT (Network Address Translation)

Translation mechanism that rewrites IP addresses (and often ports) in packet headers, enabling private address spaces to communicate with public networks and allowing address conservation.

## Purpose

- **Address Shortage**: IPv4 address depletion; reuse private addresses across organizations
- **Security**: Hide internal network structure from external hosts
- **Flexibility**: Change internal addressing without external impact
- **Mobility**: Hosts can move between networks with different address space

## Types

### Static NAT
- **1:1 mapping**: One internal IP → one external IP
- **Bidirectional**: Both directions (inside-out, outside-in)
- **Use**: Server publishing, permanent mappings
- **Overhead**: One external IP per internal IP

### Dynamic NAT
- **Pool-based**: Internal IPs → pool of external IPs
- **Automatic allocation**: First available address in pool
- **Bidirectional** with timeout: Connection-based retention
- **Efficiency**: Fewer external IPs than internal hosts
- **Limitation**: No external-to-internal initiation (no return traffic)

### NAPT (Network Address Port Translation)
- **Many-to-one**: Multiple internal IPs → single external IP
- **Port multiplexing**: Unique (src-port, protocol) tuple per session
- **Unidirectional**: Only inside-initiated connections
- **Address overflow**: Handles 64K simultaneous connections per external IP
- **Common**: Home/small office, ISP gateways

### Easy IP (Overloading/PAT)
- **Simplified NAPT**: No explicit pool; uses interface IP
- **Auto-mode**: Assigns ports dynamically without rules
- **Configuration**: Single command vs. detailed pool/rule setup
- **Common**: Branch offices, dynamic interfaces (PPP, DHCP)

### NAT Server
- **Static inbound**: Map external IP/port → internal server IP/port
- **Reverse NAT**: External clients reach internal services
- **Use**: Publishing web servers, mail servers without changing internal IP
- **Configuration**: Port forwarding rules

## Address Terminology

- **Inside local**: Internal IP (private, e.g., 192.168.1.10)
- **Inside global**: External IP assigned (public, e.g., 203.0.113.50)
- **Outside local**: External host IP as seen internally (usually same as outside global)
- **Outside global**: External host IP as seen from internet

## Working Mechanism

1. **Outbound** (Inside-to-Outside):
   - Source IP (inside local) → Inside global
   - Source port (dynamic NAT/NAPT) → translated port
   - Update translation table
   - Forward packet

2. **Inbound** (Outside-to-Inside):
   - Destination IP (inside global) → Inside local
   - Destination port → original port
   - Lookup translation table
   - Reverse translate

3. **Session-based**: Table entries expire on idle timeout (typically 300-3600s)

## Limitations

- **Bidirectional**: Cannot initiate from outside (except NAT Server)
- **Performance**: CPU-intensive (packet inspection, address rewriting)
- **Application issues**: Some protocols embed addresses (FTP, SIP); need ALG (Application Layer Gateway)
- **MTU/checksum**: Packet size may increase; TCP/UDP checksums recomputed
- **Traceability**: Difficult to debug; multiple internal hosts appear as one

## Related Concepts

- [[NAPT (Network Address Port Translation)]]
- [[Static NAT]]
- [[Dynamic NAT]]
- [[NAT Server]]
- [[Easy IP]]
- [[IP Address]]
- [[Port]]
- [[TCP/IP]]
