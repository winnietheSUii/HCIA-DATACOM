# NAT Server

Static inbound mapping that allows external clients to access internal servers by rewriting destination address and port. Also called port forwarding or reverse NAT.

## Purpose

- **Server publishing**: Make internal servers reachable from outside without changing their internal IP
- **External appearance**: Internal server appears to be at external IP:port
- **Single IP**: Expose multiple services (web, mail, DNS) via different ports on one external IP

## How It Works

```
External client: 8.8.8.8:54321 → 203.0.113.50:80
NAT Server rule: 203.0.113.50:80 → 192.168.1.50:80

Internal rewrite: 8.8.8.8:54321 → 192.168.1.50:80
Server replies: 192.168.1.50 → 8.8.8.8:54321
NAT Server reverse: 203.0.113.50 → 8.8.8.8 (source NAT)

Result: Client sees 203.0.113.50:80 as server address
```

## Configuration (Huawei VRP)

```
# Single port forwarding
[Router-nat] nat server protocol tcp global 203.0.113.50 80 inside 192.168.1.50 80

# Multiple services on different ports (same external IP)
[Router-nat] nat server protocol tcp global 203.0.113.50 443 inside 192.168.1.50 443
[Router-nat] nat server protocol tcp global 203.0.113.50 25  inside 192.168.1.100 25

# Verify
display nat server
```

## Examples

### Web Server Publishing

```
Internal: 192.168.1.50:80 (Apache)
External: 203.0.113.50:80

External user accesses: http://203.0.113.50
Routed to: 192.168.1.50 (internal)
```

### Mail Server with Multiple Services

```
External IP: 203.0.113.50

SMTP (25)   → 203.0.113.50:25   → 192.168.1.100:25
POP3 (110)  → 203.0.113.50:110  → 192.168.1.100:110
IMAP (143)  → 203.0.113.50:143  → 192.168.1.100:143

All map to same internal server
```

### Port Remapping

```
Internal: 192.168.1.50:8080 (non-standard web port)
External: 203.0.113.50:80 (standard HTTP)

External user: http://203.0.113.50:80
Mapped to: 192.168.1.50:8080
```

## Verification

```
[Router] display nat server
  Protocol  Global Address         Inside Address        Status
  --------  +--------------------+--------------------  +--------
    tcp     203.0.113.50:80        192.168.1.50:80        active
    tcp     203.0.113.50:443       192.168.1.50:443       active
    tcp     203.0.113.50:25        192.168.1.100:25       active
```

## Advantages

- **Bidirectional**: Both incoming (external) and outgoing traffic works
- **Server transparency**: Internal IP unchanged; no reconfiguration needed
- **Port flexibility**: Can remap ports (external 80 → internal 8080)
- **Multiple services**: Many servers behind one external IP

## Disadvantages

- **Management overhead**: One rule per service/port
- **Scale limitation**: Complex with hundreds of servers
- **DNS dependency**: External clients need external IP or DNS name
- **Firewall coordination**: Must allow inbound on published ports

## ALG (Application Layer Gateway) Interaction

Some protocols embed IP addresses in payloads:
- **FTP**: Active mode embeds server IP in PORT command
- **SIP**: Contact headers include internal IP
- **H.323**: Call setup messages reference IPs

Solution: ALG inspects and rewrites embedded addresses

## Related Concepts

- [[NAT]]
- [[Static NAT]]
- [[Port]]
- [[Port Forwarding]]
- [[Firewall]]
- [[Application Layer Gateway]]
