# TCP/IP Model

A 4-layer practical model that forms the basis of the modern Internet.

## Layers

| Layer | Function | Protocols |
|-------|----------|-----------|
| Application | User services, data representation | HTTP, FTP, DNS, DHCP, SMTP, Telnet |
| Transport | End-to-end communication | TCP, UDP |
| Internet | Logical addressing, routing | IP, ICMP, ARP, RARP |
| Network Access | Physical transmission, framing | Ethernet, PPP, Wi-Fi |

## Key Protocols

### Transport Layer
- **TCP** - Connection-oriented, reliable (3-way handshake)
- **UDP** - Connectionless, fast (no guarantee)

### Internet Layer
- **IP** - Logical addressing, packet forwarding
- **ICMP** - Error reporting, diagnostics (ping)
- **ARP** - IP to MAC address resolution

## TCP vs UDP
| Feature | TCP | UDP |
|---------|-----|-----|
| Connection | Yes | No |
| Reliability | Guaranteed | Best effort |
| Ordering | Yes | No |
| Speed | Slower | Faster |
| Use case | HTTP, FTP, Email | DNS, VoIP, Streaming |

## Related Concepts
- [[OSI-Model]]
- [[Encapsulation]]

---
*Referenced in: [[Module-02-Network-Reference-Model#2.2 Network Reference Model and Standard Protocols]]*