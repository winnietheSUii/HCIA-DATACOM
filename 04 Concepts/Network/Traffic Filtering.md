# Traffic Filtering

Process of examining packets and allowing or blocking them based on rules ([[ACL]]).

## Mechanisms
- **ACL**: Rule-based filtering (most common)
- **MAC filtering**: Layer 2 by MAC address (older; limited)
- **IP filtering**: Layer 3 by IP (standard)

## Directions
- **Inbound**: On ingress interface; filters entering traffic
- **Outbound**: On egress interface; filters leaving traffic

## Application Points
- **Interface**: Apply ACL to port (inbound/outbound)
- **VTY lines**: Control remote access (Telnet, SSH)
- **RADIUS**: Restrict AAA server traffic
- **Rate limiting**: Combine with queuing policies

## Processing Impact
- **CPU load**: Every packet checked against rules (slow for large ACLs)
- **Optimization**: Place frequently-matched rules first
- **Hardware offload**: Modern switches use ASIC (faster)

## Common Scenarios

### Allow Web Traffic Only
```
rule 1 permit tcp destination-port eq 80
rule 2 permit tcp destination-port eq 443
rule 3 deny ip source any destination any
```

### Block Specific Service
```
rule 1 deny tcp destination-port eq 23  ; Telnet
rule 2 permit ip source any destination any
```

### Rate Limit P2P
```
rule 1 deny tcp destination-port range 6000 7000
rule 2 permit ip source any destination any
```

## Testing
- **Simulate traffic**: ping, tracert, telnet
- **Verify blocking**: Check syslog for denied packets
- **Monitor counters**: `display acl <name>` shows permit/deny counts

## Related
- [[ACL]]
- [[Basic ACL]]
- [[Extended ACL]]
