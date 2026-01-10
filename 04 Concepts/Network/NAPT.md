# NAPT (Network Address Port Translation)

Translation of both IP address and [[Port]] number. Allows many internal IPs to share a single external IP by multiplexing connections via unique port combinations. Also called PAT (Port Address Translation) or IP Masquerading.

## Characteristics

- **Many-to-one**: Many internal IPs → single external IP
- **Port multiplexing**: Each session identified by (src-IP, src-port, protocol, dst-IP, dst-port)
- **Unidirectional**: Only inside-initiated connections work; outside cannot initiate to internal
- **Scalable**: Supports 65K simultaneous sessions per external IP (2^16 ports)
- **Stateful**: Maintains translation state for each connection

## How It Works

1. **Outbound**:
   ```
   Internal: 192.168.1.10:52341 → dst-IP:80
   NAT assigns dynamic source port (e.g., 12345)
   External: 203.0.113.50:12345 → dst-IP:80
   ```

2. **Return traffic**:
   ```
   External reply: dst-IP:80 → 203.0.113.50:12345
   Router looks up port 12345 in translation table
   Rewrites destination: → 192.168.1.10:52341
   Forwards internally
   ```

3. **Multiple sessions**:
   ```
   Internal 192.168.1.10:52341 → 203.0.113.50:10001
   Internal 192.168.1.20:8080  → 203.0.113.50:10002
   Internal 192.168.1.30:443   → 203.0.113.50:10003
   
   All share single external IP; unique ports differentiate.
   ```

## Configuration (Huawei VRP)

```
[Router-nat] acl 2001
[Router-acl2001] rule 1 permit source 192.168.1.0 0.0.0.255
[Router-acl2001] quit

[Router-nat] nat outbound 1 match-ace acl-2001 interface GigabitEthernet0/0/0 overload

# Verify
display nat outbound
display nat session
display nat statistics
```

## Use Cases

- **Home networks**: All internal devices share single ISP IP
- **Small offices**: Branch site hiding behind one external IP
- **ISP gateways**: Thousands of subscribers behind few external IPs
- **Mobile networks**: RAN devices using NAPT to external network

## Port Handling

- **Static mappings**: Some ports reserved (e.g., 80, 443 for servers via NAT Server)
- **Dynamic allocation**: Ephemeral ports 49152-65535 used for internal source ports
- **Overlap risk**: 64K limit; more than 65K simultaneous sessions → allocation failure

## Limitations

- **Inbound not possible**: External host cannot initiate to internal host
- **ALG required**: Protocols embedding IP/port (FTP, SIP, H.323) need Application Layer Gateway
- **Fragmentation**: Packets may increase in size; MTU issues possible
- **Debugging**: Hard to trace issues; all internal hosts appear as one external

## Comparison: Dynamic NAT vs NAPT

| Feature | Dynamic NAT | NAPT |
|---------|-------------|------|
| Address ratio | Few:Many (e.g., 10:100) | One:Many (1:64K sessions) |
| Port rewriting | No | Yes |
| Efficiency | Good | Excellent |
| Return sessions | Yes (bidirectional) | No (unidirectional) |
| External IPs needed | Several | One |
| Configuration | Pool-based | Interface-based (easier) |

## Related Concepts

- [[NAT]]
- [[Easy IP]]
- [[Port]]
- [[Session]]
- [[Translation Table]]
