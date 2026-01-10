# Translation Table

Internal data structure maintained by [[NAT]] devices to track bidirectional address/port mappings for active sessions. Enables reverse translation of return traffic.

## Entry Structure

Each entry contains:

```
Inside Local    Inside Global    Outside Local    Outside Global    Protocol    Timeout
192.168.1.10    203.0.113.50     8.8.8.8          8.8.8.8           TCP         300s
:52341          :10001           :80              :80
```

**Meaning**: Internal host 192.168.1.10:52341 is currently mapped to external 203.0.113.50:10001 for communication with 8.8.8.8:80

## Lookup Process

### Outbound (Inside-to-Outside)
1. Packet arrives: src=192.168.1.10:52341, dst=8.8.8.8:80
2. Lookup table by (inside-local, inside-global dest)
3. Find or create entry: assign external port
4. Rewrite: src=203.0.113.50:10001, dst=8.8.8.8:80
5. Forward packet
6. Reset idle timeout to 300s

### Inbound (Outside-to-Inside)
1. Reply arrives: src=8.8.8.8:80, dst=203.0.113.50:10001
2. Lookup table by (outside IP, outside port)
3. Find entry: mapped to 192.168.1.10:52341
4. Rewrite: src=8.8.8.8:80, dst=192.168.1.10:52341
5. Forward packet
6. Reset idle timeout

## Timeout Behavior

- **Active**: While traffic flows, timer resets on every packet
- **Idle**: No packets for N seconds (default 300s typical)
- **Expiration**: Entry deleted; external address/port returns to pool
- **Connection-specific**: TCP knows connection end (FIN/RST); may expire sooner

## Management

### Viewing Table (Huawei VRP)

```
[Router] display nat session all
  Session ID      Inside IP:Port  Outside IP:Port  Protocol  Duration
  +-----------+---+----------+---+-------+--------+---------
  1234        192.168.1.10:52341  8.8.8.8:80  TCP      120s
  1235        192.168.1.20:443    8.8.4.4:443 TCP      60s
```

### Clearing Entries

```
[Router] reset nat session all
[Router] reset nat session inside 192.168.1.10
```

### Statistics

```
[Router] display nat statistics
  Total sessions: 1250
  TCP sessions: 1100
  UDP sessions: 150
  Max sessions: 64000
```

## Size and Performance

- **Memory**: Each entry ~100-200 bytes; 64K sessions = 6-13 MB
- **Lookup speed**: Hash-based; O(1) average; critical for line-rate forwarding
- **Scalability**: Hardware NAT in ASIC; software NAT limited to 10-100 Mbps

## Issues and Debugging

### Translation Failure
- Causes: Invalid ACL, pool exhausted, interface down
- Debug: `display nat session`, check error counters

### Bidirectional Failure
- Symptom: Outbound works; return traffic lost
- Reason: NAPT timeout before return; NAT requires bidirectional entry
- Solution: Increase timeout; use Dynamic NAT for bidirectional

### Port Exhaustion
- Symptom: New connections fail; "translation resource unavailable"
- Reason: >64K sessions per external IP
- Solution: Add more external IPs; implement connection pooling

## Related Concepts

- [[NAT]]
- [[Session]]
- [[Port]]
- [[NAPT (Network Address Port Translation)]]
- [[IP Address]]
