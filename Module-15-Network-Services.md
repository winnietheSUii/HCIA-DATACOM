# Module 15: Network Services and Applications

## 15.1 File Transfer

### 15.1.1 File Transfer Protocols

Two main protocols for transferring files across networks:

| Protocol | Port | Authentication | Encryption | Use Case |
|----------|------|----------------|-----------|----------|
| [[FTP]] | 21 (control), 20 (data) | Username/password | None | Legacy bulk transfer |
| [[TFTP]] | 69 | None | None | Boot images, firmware |

### 15.1.2 FTP (File Transfer Protocol)

**Characteristics**:
- Separate control (TCP 21) and data (TCP 20) connections
- Stateful; maintains session
- Two modes: Active (server-initiated data) and Passive (client-initiated data)
- Unencrypted; credentials in clear text
- Supports directory navigation, file listing, resume

**Active Mode Issues**:
- Server initiates data connection to client
- Firewall problems; requires exception for inbound
- NAT incompatible without [[Application Layer Gateway]]

**Passive Mode**:
- Client initiates both control and data connections
- Firewall-friendly (client-initiated outbound)
- Requires ALG for NAT environments

**Configuration (Huawei VRP)**:

```
[Router] ftp server enable

[Router-aaa] local-user ftpuser password cipher Ftp@123
[Router-aaa] local-user ftpuser service-type ftp

# Verify
display ftp server status
```

**NAT Requirement**:
- FTP embeds IP addresses in `PORT` commands
- Example: `PORT 192,168,1,10,200,5` = IP 192.168.1.10, port 200*256+5
- ALG must rewrite these for NAT to work

### 15.1.3 TFTP (Trivial File Transfer Protocol)

**Characteristics**:
- Minimal protocol; no authentication or directory listing
- UDP-based (port 69); unreliable; application-level retries
- Simple state machine; suitable for embedded systems
- Designed for small files (config, firmware, boot images)
- NetASCII (text) or Octet (binary) modes

**Common Use**:
- PXE boot (bare metal provisioning)
- Router firmware upgrades
- Network device configuration download
- DHCP-provided TFTP server for boot

**How It Works**:

```
1. Client: RRQ (Read Request) "send image.bin"
2. Server: DATA block 1 (512 bytes)
3. Client: ACK block 1
4. Server: DATA block 2
5. Client: ACK block 2
... (repeat until <512 bytes received)
6. Client: ACK final → complete
```

**Configuration (Huawei VRP)**:

```
[Router] tftp server enable

# Root directory
[Router] tftp-server root /flash

# Verify
display tftp server status

# As client
[Router] tftp 203.0.113.10 get image.bin flash:/update.bin
[Router] tftp 203.0.113.10 put running-config backup.conf
```

**Advantages**:
- Minimal overhead; works on minimal hardware
- No firewall complexity (unlike FTP active mode)
- Fast for small files

**Disadvantages**:
- No authentication (security risk)
- UDP unreliable; slow for large files
- No encryption (firmware/config exposed)
- Obsolete; replaced by SFTP

---

## 15.2 Telnet

### 15.2.1 Purpose and Characteristics

[[Telnet]] (TCP 23) = remote login protocol; provides interactive terminal session.

**Characteristics**:
- Interactive; behaves like local terminal
- Unencrypted credentials and session
- Legacy; now obsolete for security reasons
- Pre-SSH era protocol
- Common in older networks and embedded systems

### 15.2.2 Authentication

Three methods:

1. **No authentication**: Open access (dangerous)
2. **Simple password**: Single password for all users
3. **AAA**: Username/password via RADIUS/TACACS+

### 15.2.3 Configuration (Huawei VRP)

**Enable Telnet server**:

```
[Router] telnet server enable
```

**Configure VTY lines** (virtual terminal):

```
[Router] line vty 0 4
[Router-line-vty0-4] authentication-mode aaa
[Router-line-vty0-4] protocol telnet
[Router-line-vty0-4] quit

[Router] line vty 0 4
[Router-line-vty0-4] set authentication password cipher Telnet@123
```

**AAA authentication**:

```
[Router-aaa] local-user admin password cipher Admin@123
[Router-aaa] local-user admin service-type telnet
[Router-aaa] quit
```

**Verify**:

```
[Router] display telnet server status
  Telnet server: enabled
  VTY lines: 0-4 (5 sessions max)
  
[Router] display line vty
```

**Test**:

```
[Router] telnet 192.168.1.1
Connected to 192.168.1.1
<Huawei Router>
Username: admin
Password: ****
[Router] display version
```

### 15.2.4 SSH Alternative

**SSH (Secure Shell)**:
- Encrypted login and session
- Public key authentication available
- Port 22 (standard)
- Replaces Telnet in modern networks

**Configuration (Huawei VRP)**:

```
[Router] ssh server enable
[Router] rsa local-key-pair create

[Router] line vty 0 4
[Router-line-vty0-4] protocol ssh telnet
[Router-line-vty0-4] authentication-mode aaa
```

### 15.2.5 Security Issues

- **Plaintext credentials**: Packet sniffer captures username/password
- **Session hijacking**: No encryption; session can be intercepted
- **Eavesdropping**: All commands and output visible

**Best practices**:
1. Disable Telnet; use SSH instead
2. If Telnet unavoidable: restrict to trusted networks
3. Monitor access logs
4. Use strong passwords (12+ chars, complexity)

---

## 15.3 DHCP

### 15.3.1 Purpose and DORA Process

[[DHCP]] (UDP 67 server, 68 client) = automatic IP address assignment with network configuration.

**Purpose**:
- Eliminate manual IP assignment
- Centralize network configuration (gateway, DNS, NTP)
- Support host mobility (move between networks, get new IP)
- Scale to thousands of clients

**DORA Process** (Discover → Offer → Request → Acknowledge):

```
1. DISCOVER: Client broadcasts "I need an IP"
   src=0.0.0.0:68, dst=255.255.255.255:67

2. OFFER: Server responds with available IP, lease time
   src=server:67, dst=broadcast:68
   Offered IP: 192.168.1.100, Lease: 7 days

3. REQUEST: Client confirms selected offer
   src=0.0.0.0:68, dst=255.255.255.255:67
   Requested IP: 192.168.1.100

4. ACK: Server confirms; sends full config
   src=server:67, dst=client:68
   IP: 192.168.1.100
   Gateway: 192.168.1.1
   DNS: 8.8.8.8, 8.8.4.4
   Lease: 604800 (7 days)
```

### 15.3.2 DHCP Options

| Option | Name | Purpose | Example |
|--------|------|---------|---------|
| 3 | Router | Default gateway | 192.168.1.1 |
| 6 | DNS | DNS servers | 8.8.8.8, 8.8.4.4 |
| 15 | Domain Name | Domain to append | example.com |
| 51 | IP Address Lease Time | Duration in seconds | 604800 (7 days) |
| 66 | TFTP Server Name | Boot server | 192.168.1.5 |
| 67 | Boot File Name | Image to load | pxelinux.0 |
| 150 | TFTP Server IP | (Cisco Phones) | 192.168.1.10 |
| 184 | Voice VLAN | (Cisco IP Phones) | 100 |

### 15.3.3 Configuration (Huawei VRP)

**Enable DHCP**:

```
[Router] dhcp enable
```

**Create IP pool**:

```
[Router] ip pool Sales-VLAN
[Router-pool-Sales-VLAN] network 192.168.10.0 mask 255.255.255.0
[Router-pool-Sales-VLAN] gateway-list 192.168.10.1
[Router-pool-Sales-VLAN] dns-list 8.8.8.8 8.8.4.4
[Router-pool-Sales-VLAN] domain-name example.com
[Router-pool-Sales-VLAN] lease day 7 hour 0 minute 0
[Router-pool-Sales-VLAN] quit
```

**Assign pool to interface**:

```
[Router] interface GigabitEthernet0/0/1
[Router-GigabitEthernet0/0/1] dhcp select global
```

**Exclude addresses** (for static assignments):

```
[Router] excluded-ip-address 192.168.10.1 192.168.10.10
[Router] excluded-ip-address 192.168.10.200 192.168.10.254
```

**Static binding** (reserve for known device):

```
[Router] static-bind ip-address 192.168.10.50 mac-address 00-11-22-33-44-55
```

**Verify**:

```
[Router] display dhcp pool
  Pool name: Sales-VLAN
  Network: 192.168.10.0/24
  Gateway: 192.168.10.1
  DNS: 8.8.8.8, 8.8.4.4
  Lease: 7 days
  
[Router] display dhcp server statistics
  Total: 100
  Assigned: 45
  Available: 55
  
[Router] display dhcp server ip-in-use
  IP Address        MAC Address        Lease    Remaining
  192.168.10.50     00-11-22-33-44-55  7 days   3 days
```

### 15.3.4 Lease Renewal

**Lease timeline**:
- **T0** (lease granted): 0% - Client uses address
- **T1** (50% elapsed): Client sends RENEW (unicast to server)
- **T2** (87.5% elapsed): If T1 fails, REBIND (broadcast to any server)
- **Expiration**: If no renewal, IP released; client loses address

**Example** (7-day lease):
- T0 = day 0 (lease granted)
- T1 = day 3.5 (renewal attempt)
- T2 = day 6.125 (rebind if renewal failed)
- Expiration = day 7

### 15.3.5 DHCP Relay (Multi-subnet)

For subnets without DHCP server; relay requests to server:

```
[Router] interface GigabitEthernet0/0/2
[Router-GigabitEthernet0/0/2] dhcp select relay
[Router-GigabitEthernet0/0/2] dhcp relay server-ip 192.168.100.10 #Server IP
```

Behavior:
- DHCP DISCOVER from 192.168.20.0/24 → relayed to 192.168.100.10
- Server responds; relay forwards to client
- Supports multiple servers (redundancy)

### 15.3.6 Security and Troubleshooting

**Security threats**:
- **Rogue DHCP**: Attacker server offers malicious gateway/DNS
  - Mitigation: DHCP snooping, DHCP guard on switches
- **DHCP exhaustion**: Attacker requests all pool IPs (DoS)
  - Mitigation: Rate limiting, per-interface allocation limits

**Troubleshooting**:
```
# Check if client gets IP
[Client] ipconfig /all

# Check DHCP server status
[Router] display dhcp server statistics
[Router] display dhcp server ip-in-use

# Verify relay (if using)
[Router] display dhcp relay

# Test with debug
[Router] debug dhcp
```

---

## 15.4 HTTP

### 15.4.1 Basics and Methods

[[HTTP]] (TCP 80, HTTPS 443) = stateless protocol for retrieving web resources.

**Request-Response**:
```
1. Client: TCP connection to port 80
2. Client: HTTP GET /index.html HTTP/1.1
3. Server: HTTP/1.1 200 OK + HTML body
4. Connection close (or keep-alive)
```

**HTTP Methods**:

| Method | Purpose | Safe | Idempotent | Typical Use |
|--------|---------|------|------------|------------|
| GET | Retrieve | Yes | Yes | Fetch web page |
| POST | Submit data | No | No | Form submission |
| PUT | Replace | No | Yes | REST API update |
| DELETE | Remove | No | Yes | REST API delete |
| HEAD | GET without body | Yes | Yes | Check availability |

### 15.4.2 Status Codes

| Range | Meaning | Examples |
|-------|---------|----------|
| 1xx | Informational | 100 Continue |
| 2xx | Success | 200 OK, 201 Created |
| 3xx | Redirection | 301 Moved, 304 Not Modified |
| 4xx | Client error | 400 Bad Request, 404 Not Found, 403 Forbidden |
| 5xx | Server error | 500 Internal Error, 503 Unavailable |

### 15.4.3 HTTPS (HTTP Secure)

**Encryption**:
- HTTP over TLS (port 443)
- Prevents eavesdropping and tampering
- Requires X.509 certificate

**Deployment**:
```
Server certificate: www.example.com (public key)
Client verifies: Certificate CA chain
Negotiates: Symmetric key for session
All HTTP traffic encrypted
```

### 15.4.4 Web Server Publishing (NAT)

**Scenario**: Internal web server 192.168.1.50:80 published as 203.0.113.50:80

```
[Router-nat] nat server protocol tcp global 203.0.113.50 80 inside 192.168.1.50 80
[Router-nat] nat server protocol tcp global 203.0.113.50 443 inside 192.168.1.50 443

display nat server
```

**External access**:
```
Browser: http://203.0.113.50
         or https://203.0.113.50 (if certificate valid)
Routed to: 192.168.1.50:80/443
```

### 15.4.5 Performance and Optimization

**Keep-Alive** (HTTP/1.1):
- Reuse TCP connection for multiple requests
- Reduces connection overhead
- `Connection: keep-alive` header

**Compression**:
- gzip/deflate encoding
- Server: `Content-Encoding: gzip`
- Reduces bandwidth 50-80%

**Caching**:
- ETags: Version identifier; avoid re-download if unchanged
- Last-Modified: Timestamp for cache validation
- Cache-Control: max-age, no-cache directives

**CDN** (Content Delivery Network):
- Cache copies geographically distributed
- Reduce origin server load
- Lower latency for users

---

## 15.5 DNS

### 15.5.1 Purpose and Query Process

[[DNS]] (UDP 53 primary, TCP 53 secondary) = hierarchical distributed system translating domain names to IP addresses.

**Purpose**:
- Name resolution: www.example.com → 203.0.113.50
- Reverse resolution: 203.0.113.50 → www.example.com
- Service discovery: MX (mail), SRV (services)
- Load balancing: Round-robin (same domain → multiple IPs)

**Recursive Resolution Process**:

```
Client: "What is www.example.com?"
  ↓
Recursive Resolver (ISP): "I'll find out"
  ↓
Root Nameserver (13 globally): "Ask .com TLD server"
  ↓
.com TLD Server: "Ask example.com authoritative"
  ↓
example.com Authoritative: "203.0.113.50"
  ↓
Resolver caches, returns to client: 203.0.113.50
```

**Time**: Typically 100-500ms (multi-hop, first query); <10ms (cached)

### 15.5.2 DNS Records

| Record | Type | Purpose | Example |
|--------|------|---------|---------|
| A | IPv4 | Map domain to IP | www.example.com → 203.0.113.50 |
| AAAA | IPv6 | Map domain to IPv6 | www.example.com → 2001:db8::1 |
| CNAME | Alias | Canonical name | mail.example.com → mailserver.example.com |
| MX | Mail | Mail server | example.com → mail.example.com (priority 10) |
| NS | Nameserver | Authoritative server | example.com → ns1.example.com |
| SOA | Authority | Zone authority | example.com contact, serial, refresh |
| TXT | Text | Text records | example.com → "v=spf1 include:..." (SPF) |
| PTR | Pointer | Reverse lookup | 50.113.0.203.in-addr.arpa → www.example.com |

### 15.5.3 Configuration (Huawei VRP)

**As DNS Client (Resolver)**:

```
[Router] dns resolve
[Router-dns] dns server 8.8.8.8
[Router-dns] dns server 8.8.4.4
[Router-dns] domain-name example.com

display dns status
```

**As Authoritative Server** (Huawei):

```
[Router] dns enable

[Router-dns] zone example.com
[Router-zone-example.com] record a www 203.0.113.50
[Router-zone-example.com] record a mail 203.0.113.100
[Router-zone-example.com] record mx 10 mail
[Router-zone-example.com] record cname alias www

display dns zone
display dns record
```

**Query types**:
```
Authoritative (AA=1): Server has zone file; direct answer
Non-authoritative (AA=0): Cached answer; may be stale
```

### 15.5.4 Caching and TTL

**TTL** (Time To Live) = duration a record is cached.

**Example**:
```
DNS response: www.example.com → 203.0.113.50
TTL: 86400 (24 hours)

Client caches: Next 24 hours, responses from cache (<10ms)
After 24h: Cache expires; new query to DNS server
```

**Impact**:
- Low TTL (300s): Frequent queries; low caching; slower
- High TTL (86400s): Stale data if server IP changes; faster

### 15.5.5 Verification and Troubleshooting

**Test resolution**:

```
[Router] ping www.example.com
Ping www.example.com (203.0.113.50)

[Router] nslookup www.example.com
Address: 8.8.8.8
Name: www.example.com
Address: 203.0.113.50

[Router] nslookup 203.0.113.50  # Reverse lookup
Name: www.example.com
Address: 203.0.113.50
```

**DNS cache stats**:

```
[Router] display dns statistics
  Query requests: 1250
  Cache hits: 950 (76%)
  Cache misses: 300 (24%)
  Server responses: 300
```

### 15.5.6 Security Considerations

**DNS Poisoning**:
- Attacker injects false records
- Mitigation: DNSSEC (cryptographic signatures)

**DNS Amplification DDoS**:
- Attacker uses DNS servers to flood victims
- Large response to small query
- Mitigation: Disable recursive queries to external clients

---

## 15.6 NTP

### 15.6.1 Purpose and Stratum Hierarchy

[[NTP]] (UDP 123) = synchronizes clocks across network devices to accurate time source.

**Purpose**:
- Time synchronization (millisecond accuracy across devices)
- Accurate logging and event correlation
- Certificate validation (SSL/TLS depends on correct time)
- Security: Kerberos, OAuth require time sync
- Audit trail (legal compliance: financial, medical records)

**Stratum Levels** (hierarchical):

```
Stratum 0:  Atomic clock, GPS, radio (authoritative source)
Stratum 1:  Directly connected to Stratum 0 (highly accurate, ~1ms)
Stratum 2:  Synced from Stratum 1 (good accuracy, ~10ms)
Stratum 3:  Synced from Stratum 2 (acceptable, ~100ms)
...
Stratum 16: Unsynchronized (invalid; not used)

Accuracy decreases with each stratum level.
```

### 15.6.2 How NTP Works

**Timestamp Exchange**:

```
Client sends:       Transmit time (T1)
Server responds:
  Originate:        T1 (client's send time)
  Receive:          T2 (server received at)
  Transmit:         T3 (server sent at)
Client receives:    Local time T4

Calculation:
  Round-trip delay:   (T4 - T1) - (T3 - T2)
  Clock offset:       [(T2 - T1) + (T3 - T4)] / 2
  
Adjust local clock by offset
```

**Typical accuracy**:
- LAN (same switch): ±10ms
- Internet: ±100ms
- Public internet: ±500ms

### 15.6.3 Configuration (Huawei VRP)

**Enable NTP**:

```
[Router] ntp-service enable
```

**As NTP Client** (sync from servers):

```
[Router] ntp-service server 203.0.113.10 source GigabitEthernet0/0/0
[Router] ntp-service server 203.0.113.11 source GigabitEthernet0/0/0 prefer

# Multiple servers for redundancy; "prefer" = highest priority
```

**As NTP Server** (provide time to others):

```
[Router] ntp-service server listen ip 192.168.1.1
[Router] ntp-service server listen port 123
[Router] ntp-service enable-authentication
```

**Authentication** (prevent spoofing):

```
[Router] ntp-service authentication enable
[Router] ntp-service authentication-key 1 password SecretKey123
[Router] ntp-service server 203.0.113.10 authentication key 1
```

**Verify**:

```
[Router] display ntp-service status
  NTP enabled
  Current time: 2025-01-10 14:23:45
  Sync status: Synchronized (stratum 3)
  Last sync: 10 seconds ago
  Time source: 203.0.113.10

[Router] display ntp-service session
  Address          Status        Last Receive  Poll Interval
  203.0.113.10     synchronized  8s            64s
  203.0.113.11     candidate     120s          128s
```

### 15.6.4 Typical Deployment

```
Public Internet NTP Pool (Stratum 1-2, e.g., pool.ntp.org)
       ↓
ISP NTP Server (Stratum 2)
       ↓
Corporate Primary NTP Server (Stratum 3)
       ↓
Backup NTP Server (Stratum 3)
       ↓
Network devices (routers, switches, servers, workstations)
```

**Best practices**:
1. **Multiple sources**: 3-4 servers for redundancy
2. **Prefer lower stratum**: Set `prefer` on most accurate
3. **Monitor sync**: Alert if stratum > 10 or sync lost
4. **Restrict access**: Allow NTP queries only from trusted networks
5. **VRF**: If using VRFs, NTP respects VRF routing

### 15.6.5 Security Considerations

**DDoS amplification**:
- NTP responses can be much larger than requests
- Attacker spoofs victim IP; sends NTP requests to servers
- Servers amplify responses to victim
- Mitigation: Restrict monlist/queries to internal clients

**Time jumping**:
- Sudden time change breaks certificate validation
- May invalidate logs, break scheduled tasks
- Mitigation: Monitor time changes; use gradual adjustment if needed

---

## Summary Table

| Service | Port(s) | Protocol | Purpose | Encryption |
|---------|---------|----------|---------|-----------|
| [[FTP]] | 21/20 | TCP | File transfer | None |
| [[TFTP]] | 69 | UDP | Boot/firmware | None |
| [[Telnet]] | 23 | TCP | Remote login | None |
| [[SSH]] | 22 | TCP | Secure remote login | Yes |
| [[DHCP]] | 67/68 | UDP | IP auto-config | None |
| [[DNS]] | 53 | UDP/TCP | Name resolution | None (DoH/DoT optional) |
| [[HTTP]] | 80 | TCP | Web | None |
| [[HTTPS]] | 443 | TCP | Secure web | Yes (TLS) |
| [[NTP]] | 123 | UDP | Time sync | None (auth optional) |

**Key exam concepts**:
- DHCP DORA: Discover → Offer → Request → Acknowledge
- NTP stratums; accuracy depends on hierarchy
- FTP vs TFTP: Stateful/full vs stateless/minimal
- HTTP status codes: 1xx=info, 2xx=success, 3xx=redirect, 4xx=client error, 5xx=server error
- DNS: Recursive vs authoritative; TTL caching
- File transfer: FTP (stateful, auth), TFTP (stateless, boot), SSH/SFTP (encrypted)
- Security: Disable Telnet (use SSH); validate certificates; restrict access
