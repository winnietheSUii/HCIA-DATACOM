# ACL Rule

Single line in an [[ACL]] specifying an action (permit/deny) and match criteria (source IP, protocol, port, etc.).

## Structure

**Basic ACL rule**:
```
rule <sequence> <action> source <IP> <wildcard>
```

**Extended ACL rule**:
```
rule <sequence> <action> <protocol> source <src-IP> [src-port] 
    destination <dst-IP> [dst-port]
```

## Components

### Sequence Number
- Order of evaluation (1-65535)
- **First match wins**: Stops checking once matched
- Can be explicitly set or auto-assigned

### Action
- **permit**: Allow matching traffic
- **deny**: Block matching traffic

### Protocols (Extended)
- `tcp`: TCP packets only
- `udp`: UDP packets only
- `icmp`: ICMP packets only
- `ip`: Any IP protocol (default)

### IP Address & Wildcard
- **IP**: Network or host address (e.g., 192.168.1.0)
- **Wildcard**: Inverse mask; 0 = must match, 1 = ignore
  - `0.0.0.0` = exact host (e.g., 192.168.1.1)
  - `0.0.0.255` = /24 network (e.g., 192.168.1.0/24)
  - `0.0.255.255` = /16 network (e.g., 192.168.0.0/16)
  - `255.255.255.255` = any (`any`)

### Ports (TCP/UDP)
- `eq <port>`: Exact match (e.g., `eq 80`)
- `gt <port>`: Greater than
- `lt <port>`: Less than
- `range <low> <high>`: Range (e.g., `range 1024 65535`)

### TCP Flags (Advanced)
- `tcp-flag syn`: SYN flag (new connections)
- `tcp-flag ack`: ACK flag (established connections)
- Used to filter by connection state (crude traffic engineering)

## Examples

**Single rule permitting web traffic**:
```
rule 1 permit tcp source 192.168.1.0 0.0.0.255 
    destination 10.0.0.0 0.0.255.255 destination-port eq 80
```

**Deny ICMP from external**:
```
rule 2 deny icmp source 0.0.0.0 255.255.255.255 destination any
```

**Permit SSH from anywhere**:
```
rule 3 permit tcp destination-port eq 22
```

**Block high-numbered ports (potential P2P)**:
```
rule 4 deny tcp destination-port range 6000 7000
```

## Processing Order

1. **Rules evaluated top-to-bottom** (by sequence number)
2. **First match wins**: Stops at first matching rule
3. **Implicit deny**: If no rule matches, packet **denied** (dropped)

Example:
```
rule 1 permit tcp source 192.168.1.0 0.0.0.255 destination-port eq 80
rule 2 permit tcp source 192.168.2.0 0.0.0.255 destination-port eq 443
rule 3 deny ip source any destination any
```
- PC from 192.168.1.0 → port 80: Matches rule 1 → **Permitted**
- PC from 192.168.2.0 → port 443: Matches rule 2 → **Permitted**
- PC from 10.0.0.0 → any port: No match → Rule 3 → **Denied**

## Remarks

Add human-readable descriptions:
```
rule 1 remark Allow web traffic from office networks
rule 1 permit tcp source 192.168.1.0 0.0.0.255 destination-port eq 80
```

## Related
- [[ACL]]
- [[Basic ACL]]
- [[Extended ACL]]
- [[Named ACL]]
