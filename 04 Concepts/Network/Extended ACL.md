# Extended ACL

ACL type filtering traffic by **source IP, destination IP, protocol, and port**. Most comprehensive and commonly used.

## Characteristics
- **ACL numbers**: 3000-3999 (Huawei extended)
- **Match criteria**: Source/dest IP, protocol (TCP/UDP/ICMP), port, flags (TCP SYN, ACK)
- **Flexible**: Handles complex filtering needs
- **Standard for production**: Primary method in enterprise networks

## Configuration

```
[Switch] acl extended ACL_NAME
[Switch-acl-ext-ACL_NAME] rule 1 permit tcp source 192.168.1.0 0.0.0.255 
    destination 10.0.0.0 0.0.255.255 destination-port eq 80
[Switch-acl-ext-ACL_NAME] rule 2 permit tcp source any 
    destination 10.0.0.1 0.0.0.0 destination-port eq 443
[Switch-acl-ext-ACL_NAME] rule 3 deny icmp source 192.168.0.0 0.0.255.255
[Switch-acl-ext-ACL_NAME] rule 4 permit ip source any destination any
```

- `rule <seq> <action> <protocol> source <IP> [source-port] destination <IP> [dest-port]`
- Protocol: `tcp`, `udp`, `icmp`, `ip` (any)
- Port: `eq <port>`, `gt <port>`, `lt <port>`, `range <low> <high>`

## Operators (Ports)
- `eq`: Equal (exact match)
- `gt`: Greater than
- `lt`: Less than
- `range`: Range (inclusive)

## TCP Flags (Advanced)
```
rule 10 permit tcp destination-port eq 80 tcp-flag syn
```
- Matches only SYN packets (new connections)

## Use Cases
- Web/HTTPS traffic filtering (TCP port 80/443)
- DNS control (UDP port 53)
- Email restrictions (TCP port 25/587)
- VPN/SSH access (TCP port 22)
- Deny specific protocols (ICMP, IGMP)

## Examples

**Allow HTTP/HTTPS from office to web server**:
```
rule 1 permit tcp source 192.168.1.0 0.0.0.255 
    destination 10.0.0.100 0.0.0.0 destination-port range 80 443
rule 2 deny ip source any destination any
```

**Allow DNS queries**:
```
rule 1 permit udp source any destination 8.8.8.8 0.0.0.0 
    destination-port eq 53
```

**Block Telnet, Allow SSH**:
```
rule 1 deny tcp destination-port eq 23
rule 2 permit tcp destination-port eq 22
rule 3 deny ip source any destination any
```

## Related
- [[ACL]]
- [[Basic ACL]]
- [[Named ACL]]
- [[Traffic Filtering]]
