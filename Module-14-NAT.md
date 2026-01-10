# Module 14: Network Address Translation

## 14.1 NAT Overview

### 14.1.1 Purpose and Problem Statement

**Problem**: IPv4 address exhaustion; private address spaces cannot communicate with public networks.

**Solution**: [[NAT]] translates addresses in packets, enabling:
- Private addresses → public address on outbound
- Public address → private address on return
- Address conservation (65K sessions per external IP via NAPT)
- Security (hide internal network structure)

### 14.1.2 NAT Terminology

- **Inside local** (IL): Internal IP in private network (192.168.1.10)
- **Inside global** (IG): External IP assigned by ISP or provider (203.0.113.50)
- **Outside local** (OL): External host as seen from inside (usually same as OG)
- **Outside global** (OG): External host IP as seen from internet (8.8.8.8)

### 14.1.3 NAT Operation Principles

**Outbound** (Inside → Outside):
1. Packet: src=IL, dst=OG
2. NAT rewrites: src=IG, dst=OG
3. Update translation table
4. Forward to external network

**Inbound** (Outside → Inside):
1. Reply packet: src=OG, dst=IG
2. Lookup translation table
3. Rewrite: src=OG, dst=IL
4. Forward internally

**Translation table**:
- Maintains session state (src-IP, src-port, dst-IP, dst-port, protocol)
- Entries expire on idle timeout (300-3600s typical)
- Per-flow: TCP, UDP flows tracked separately

### 14.1.4 NAT Types

Four main categories with different characteristics:

| Type | Mapping | Direction | Use Case |
|------|---------|-----------|----------|
| [[Static NAT]] | 1:1 permanent | Bidirectional | Server publishing |
| [[Dynamic NAT]] | M:Few pool | Bidirectional* | Medium networks |
| [[NAPT]] | M:1 port-based | Unidirectional | High scalability |
| [[Easy IP]] | M:1 interface | Unidirectional | Branch/ISP |

*Dynamic NAT: Return traffic only within timeout

### 14.1.5 Limitations of NAT

- **Bidirectional**: Dynamic NAT/NAPT cannot receive unsolicited external traffic
- **Performance**: CPU-intensive (address/port rewriting, checksum updates)
- **ALG requirement**: Protocols embedding IPs (FTP, SIP) need Application Layer Gateway
- **Traceability**: Multiple internal hosts appear as one external; debugging difficult
- **IPv6 path**: IPv6 eliminates NAT (address space sufficient)

---

## 14.2 Static NAT

### 14.2.1 Concepts and Characteristics

[[Static NAT]] = permanent one-to-one mapping between internal IP and external IP.

**Characteristics**:
- Fixed mapping (never changes)
- Always active (mapping exists regardless of traffic)
- **Bidirectional**: Both external-initiated and internal-initiated work
- Expensive (requires one external IP per internal host)
- Predictable (same internal host always visible as same external IP)

### 14.2.2 Working Mechanism

```
Internal: 192.168.1.50 → External: 203.0.113.50

Outbound:
  External host 8.8.8.8 initiates: → 203.0.113.50
  NAT translates: src=192.168.1.50 (inside local)
  Forwards: 192.168.1.50 → 8.8.8.8

Inbound (return):
  8.8.8.8 replies: → 203.0.113.50
  NAT translates: dst=192.168.1.50 (inside local)
  Forwards to: 192.168.1.50
```

### 14.2.3 Configuration (Huawei VRP)

**Step 1**: Define which internal IPs to NAT

```
[Router] acl 2001
[Router-acl2001] rule 1 permit source 192.168.1.50 0
[Router-acl2001] quit
```

**Step 2**: Configure static mapping

```
[Router] nat static global 203.0.113.50 inside 192.168.1.50 acl 2001
```

**Step 3**: Apply to NAT interface

```
[Router] interface GigabitEthernet0/0/0
[Router-GigabitEthernet0/0/0] nat outbound 1
```

**Step 4**: Verify

```
[Router] display nat static
  Protocol  Inside IP:Port    Outside IP:Port   Status
  --------  +-------+-------  +-------+-------  +------
    tcp     192.168.1.50      203.0.113.50      active
    udp     192.168.1.50      203.0.113.50      active
```

### 14.2.4 Practical Scenarios

**Scenario 1**: Server Publishing
- Internal web server: 192.168.1.50:80
- External IP assigned: 203.0.113.50
- Static NAT maps 203.0.113.50 → 192.168.1.50
- External users access web server via 203.0.113.50

**Scenario 2**: Permanent Branch Office
- Branch router internal IP: 10.1.1.1
- HQ sees branch always as: 203.0.113.10
- VPN or static route uses consistent IP
- Simplifies HQ routing; no dynamic IP tracking

### 14.2.5 Advantages and Disadvantages

**Advantages**:
- Simple, predictable behavior
- Works bidirectionally
- No session timeout issues
- Suitable for permanent servers

**Disadvantages**:
- Requires one external IP per host (inefficient)
- Does not scale to large internal networks
- Wasteful if many internal hosts don't need external access

---

## 14.3 Dynamic NAT

### 14.3.1 Concepts

[[Dynamic NAT]] = automatic, temporary assignment from a pool of external IPs.

**Characteristics**:
- Pool-based allocation (internal IPs select from pool)
- First available IP assigned; others return to pool on timeout
- Bidirectional **within timeout window** (return traffic within idle timer)
- Efficient (fewer external IPs than internal IPs)
- Session-based (connection-dependent allocation)

### 14.3.2 Working Mechanism

```
Internal IPs:      External Pool:      Active Mapping:
192.168.1.10  →   203.0.113.50-59  →  203.0.113.50 (session 1)
192.168.1.20  →                    →  203.0.113.51 (session 2)
192.168.1.30  →                    →  203.0.113.52 (session 3)

When session 1 times out (idle):
  203.0.113.50 → returns to pool (available again)
```

**Process**:
1. Internal host initiates outbound connection (e.g., 192.168.1.10 → 8.8.8.8:80)
2. Router selects first available IP from pool (203.0.113.50)
3. Allocates IP to host; creates translation table entry
4. Returns traffic arrives at 203.0.113.50 → router reverses to 192.168.1.10
5. On idle timeout (300s typical): entry expires, IP returns to pool

### 14.3.3 Configuration (Huawei VRP)

**Step 1**: Define internal hosts to NAT (ACL)

```
[Router] acl 2001
[Router-acl2001] rule 1 permit source 192.168.1.0 0.0.0.255
[Router-acl2001] quit
```

**Step 2**: Create external IP pool

```
[Router] nat address-group 1 203.0.113.50 203.0.113.59
```

**Step 3**: Configure Dynamic NAT rule

```
[Router] nat outbound 1 match-ace acl-2001 address-group 1
```

**Step 4**: Apply to NAT interface

```
[Router] interface GigabitEthernet0/0/0
[Router-GigabitEthernet0/0/0] nat outbound 1
```

**Step 5**: Verify

```
[Router] display nat outbound
  ACL: 2001, Address Group: 1
  Address Pool: 203.0.113.50-203.0.113.59
  Allocations: 8 active, 52 available

[Router] display nat session
  Inside IP:Port    Outside IP:Port   Dst IP:Port        Protocol  Duration
  +-------+-------  +-------+-------  +-------+-------  +--------  +--------
  192.168.1.10:52341 203.0.113.50:52341 8.8.8.8:80      TCP        120s
  192.168.1.20:443    203.0.113.51:443  8.8.4.4:443     TCP        45s
```

### 14.3.4 Scenarios

**Scenario 1**: Medium office (50 internal PCs, 10 external IPs)
- Pool: 203.0.113.50-59 (10 IPs)
- At any time, 10 PCs can initiate outbound
- Others queued or wait for timeout

**Scenario 2**: Return traffic limitation
- Internal host (192.168.1.10) initiates HTTP request
- Assigned 203.0.113.50 for 300s
- If server sends unsolicited data after 300s idle → lost (no entry)

### 14.3.5 Advantages and Disadvantages

**Advantages**:
- Address efficient (ratio: 1 external per 3-10 internal)
- Scalable to medium networks
- Session-based: automatic cleanup

**Disadvantages**:
- Not truly bidirectional (timeout risk)
- Cannot receive unsolicited external connections
- No server publishing capability
- Resource intensive (translation table for all sessions)

---

## 14.4 NAPT and Easy IP

### 14.4.1 NAPT (Network Address Port Translation)

[[NAPT]] = translation of both address AND port. Allows many internal IPs to share a single external IP via port multiplexing.

**Characteristics**:
- Many-to-one mapping (unlimited internal hosts → 1 external IP)
- Port multiplexing (65K unique sessions per IP via port 1-65535)
- Unidirectional (only inside-initiated; outside cannot initiate)
- Highly scalable (ideal for ISP, branch offices, home networks)
- Stateful (maintains translation table per session)

### 14.4.2 NAPT Working Mechanism

```
Internal host: 192.168.1.10:52341 → server 8.8.8.8:80
NAT rewrites:
  src-IP: 192.168.1.10 → 203.0.113.50 (external IP)
  src-port: 52341 → 12345 (dynamic port)
  
Packet sent: 203.0.113.50:12345 → 8.8.8.8:80

Return traffic:
  8.8.8.8:80 → 203.0.113.50:12345
  NAT looks up port 12345 in translation table
  Rewrites: → 192.168.1.10:52341
  Forwards internally
```

**Port Allocation**:
- Ephemeral range: 49152-65535 (user ports)
- Dynamic allocation: Incremental or random
- Session limit: 65K simultaneous connections per external IP
- If exhausted: New connections fail ("resource unavailable")

### 14.4.3 NAPT Configuration (Huawei VRP)

**Step 1**: Define internal hosts

```
[Router] acl 2001
[Router-acl2001] rule 1 permit source 192.168.1.0 0.0.0.255
[Router-acl2001] quit
```

**Step 2**: Configure NAPT with pool

```
[Router] nat address-group 2 203.0.113.50 203.0.113.59

[Router] nat outbound 1 match-ace acl-2001 address-group 2 overload
```

**"overload"** = port translation (NAPT)

**Step 3**: Verify

```
[Router] display nat outbound
[Router] display nat session all

  Inside IP:Port      Outside IP:Port    Protocol  Dst IP:Port
  +----------+-------  +----------+------  +--------  +----------
  192.168.1.10:52341   203.0.113.50:10001  TCP      8.8.8.8:80
  192.168.1.20:8080    203.0.113.50:10002  TCP      8.8.4.4:443
  192.168.1.30:443     203.0.113.51:10003  TCP      1.1.1.1:443
```

### 14.4.4 Easy IP (Simplified NAPT)

[[Easy IP]] = NAPT using interface IP directly; no separate pool.

**Characteristics**:
- Interface-based (auto-uses interface IP)
- Minimal config (one command)
- Works with static or dynamic IP (DHCP, PPP)
- Port translation automatic
- Perfect for branches, ISP gateways

### 14.4.5 Easy IP Configuration (Huawei VRP)

**Single command**:

```
[Router] acl 2001
[Router-acl2001] rule 1 permit source 192.168.1.0 0.0.0.255
[Router-acl2001] quit

[Router] nat outbound 1 match-ace acl-2001 interface GigabitEthernet0/0/0 overload
```

**Verify**:

```
[Router] display nat outbound
  ACL: 2001, Interface: GigabitEthernet0/0/0, Overload: Yes
  Address: 203.0.113.1 (interface IP)
  
[Router] display nat session
  192.168.1.10:52341 → 203.0.113.1:10001 → 8.8.8.8:80
```

### 14.4.6 Comparison: NAPT vs Easy IP

| Feature | NAPT (Pool) | Easy IP |
|---------|------------|---------|
| Config | Complex (pool, rule) | Simple (one line) |
| External IPs | Can use multiple | Single interface IP |
| Dynamic IP | Not supported | Supported (DHCP) |
| Port range | Large (across IPs) | 64K per IP |
| Typical use | Enterprise, ISP | Branch, home, cellular |

### 14.4.7 Scenarios

**Scenario 1**: Branch office (50 PCs, single ISP link)
- ISP assigns: 203.0.113.1/24 via DHCP
- Easy IP: ACL 2001 (internal 10.0.0.0/24) → interface GigabitEthernet0/0/0 overload
- All 50 PCs share single IP; multiplexed via ports

**Scenario 2**: ISP gateway (thousands of users)
- Pool: 203.0.113.0/25 (128 external IPs)
- NAPT overload: Multiplexes thousands of users across 128 IPs

---

## 14.5 NAT Server

### 14.5.1 Purpose and Use Case

[[NAT Server]] = static inbound mapping; allows external clients to reach internal servers.

**Problem**: Static NAT and Dynamic NAT are **outbound-initiated only**.
- External client cannot initiate connection to internal host
- Need bidirectional static mapping for servers

**Solution**: NAT Server rewrites destination address (reverse NAT)
- External client: → 203.0.113.50:80
- Router rewrites: → 192.168.1.50:80 (internal server)
- Server sees external client as source; replies directly
- Router reverse-NATs reply back

### 14.5.2 Working Mechanism

```
External client: 8.8.8.8:54321 → 203.0.113.50:80

NAT Server rule:
  Global: 203.0.113.50:80
  Inside: 192.168.1.50:80

Translation:
  Destination rewrite: 203.0.113.50:80 → 192.168.1.50:80
  Source NAT: 8.8.8.8 → 203.0.113.50 (hide external source)
  
Packet forwarded to: 192.168.1.50:80 from 203.0.113.50

Server response:
  192.168.1.50 → 203.0.113.50 (appears as external client)
  Router reverse-NAT: 203.0.113.50 → 8.8.8.8
  Reply reaches: 8.8.8.8:54321 (original client)
```

### 14.5.3 Configuration (Huawei VRP)

**Syntax**:

```
nat server protocol <tcp|udp> global <ext-IP> <ext-port> inside <int-IP> <int-port>
```

**Example 1**: Single service (web server)

```
[Router] nat server protocol tcp global 203.0.113.50 80 inside 192.168.1.50 80
[Router] nat server protocol tcp global 203.0.113.50 443 inside 192.168.1.50 443
```

**Example 2**: Multiple services on different ports

```
[Router] nat server protocol tcp global 203.0.113.50 25 inside 192.168.1.100 25
[Router] nat server protocol tcp global 203.0.113.50 110 inside 192.168.1.100 110
[Router] nat server protocol tcp global 203.0.113.50 143 inside 192.168.1.100 143
```

**Example 3**: Port remapping (external 80 → internal 8080)

```
[Router] nat server protocol tcp global 203.0.113.50 80 inside 192.168.1.50 8080
```

**Verify**:

```
[Router] display nat server
  Protocol  Global Address         Inside Address        Status
  --------  +--------------------+--------------------  +--------
    tcp     203.0.113.50:80        192.168.1.50:80        active
    tcp     203.0.113.50:443       192.168.1.50:443       active
    tcp     203.0.113.50:25        192.168.1.100:25       active
```

### 14.5.4 Scenarios

**Scenario 1**: Web/Mail Server Publishing
- Web server: 192.168.1.50
- Mail server: 192.168.1.100
- External IP: 203.0.113.50

Config:
```
HTTP:   203.0.113.50:80   → 192.168.1.50:80
HTTPS:  203.0.113.50:443  → 192.168.1.50:443
SMTP:   203.0.113.50:25   → 192.168.1.100:25
POP3:   203.0.113.50:110  → 192.168.1.100:110
```

External users:
- Web: http://203.0.113.50 (or DNS www.example.com → 203.0.113.50)
- Mail: SMTP server 203.0.113.50:25

**Scenario 2**: Port Remapping
- Internal server runs on non-standard port 8080
- Publish to external port 80 (standard HTTP)

Config:
```
[Router] nat server protocol tcp global 203.0.113.50 80 inside 192.168.1.50 8080
```

External: http://203.0.113.50 works; internally routed to 192.168.1.50:8080

### 14.5.5 Verification and Troubleshooting

**Verify NAT Server rules**:

```
[Router] display nat server
[Router] display nat session
```

**Test connectivity**:

```
[Router] ping -c 4 203.0.113.50
[Router] telnet 203.0.113.50 80

# Or from external:
external$ telnet 203.0.113.50 80
```

**Common issues**:
1. **"Connection refused"**: Server not listening (check server port)
2. **"No route to host"**: Firewall blocking inbound port
3. **Server doesn't respond**: Reverse NAT not active; check rule syntax

### 14.5.6 Advantages and Disadvantages

**Advantages**:
- Bidirectional (external can initiate)
- Server transparency (internal IP unchanged)
- Port flexibility (remap as needed)
- Multiple services on one external IP

**Disadvantages**:
- Manual management per service
- Scales poorly (100+ servers unwieldy)
- Requires DNS or hardcoded external IP
- Firewall rules must allow inbound

### 14.5.7 Best Practices

1. **Server security**: Only publish necessary services
2. **Port documentation**: Keep mapping reference
3. **Firewall coordination**: Ensure inbound rules allow published ports
4. **SSL/TLS**: Encrypt sensitive services (HTTPS, not HTTP)
5. **Monitoring**: Track active sessions; detect abuse
6. **Redundancy**: Publish multiple servers if critical

---

## Summary

| NAT Type | Mapping | Bidirectional | Scalability | Common Use |
|----------|---------|---------------|-------------|------------|
| [[Static NAT]] | 1:1 | Yes | Low | Permanent servers |
| [[Dynamic NAT]] | M:Few | Yes (timeout) | Medium | Office networks |
| [[NAPT]] | M:1 | No | High | ISP, large networks |
| [[Easy IP]] | M:1 (interface) | No | High | Branches, home |
| [[NAT Server]] | 1:1 (reverse) | Yes | Low (manual) | Public servers |

**Key exam concepts**:
- NAT solves IPv4 exhaustion; enables private address reuse
- Translation table tracks bidirectional mapping; expires on timeout
- Static NAT = permanent; Dynamic NAT = pool-based; NAPT = port multiplexing
- Easy IP simplifies NAPT config; uses interface IP
- NAT Server publishes internal servers to external clients
- ALG required for protocols embedding IPs (FTP, SIP)
