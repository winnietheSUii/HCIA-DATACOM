# DNS (Domain Name System)

Hierarchical distributed system (UDP/TCP 53) that translates domain names to IP addresses.

## Purpose

- **Name resolution**: www.example.com → 203.0.113.50
- **Reverse resolution**: 203.0.113.50 → www.example.com
- **Service discovery**: Locate mail, LDAP, SRV records
- **Load balancing**: Same domain → multiple IPs (round-robin)

## Query Process: Recursive Resolution

```
Client: "What is 192.168.1.1"
  ↓
Recursive Resolver (ISP DNS): "I'll find out"
  ↓
Root Nameserver: "Ask .com authoritative server"
  ↓
.com TLD Server: "Ask example.com nameserver"
  ↓
example.com Authoritative: "203.0.113.50"
  ↓
Resolver returns 203.0.113.50 to client
```

## DNS Records

| Record Type | Purpose | Example |
|-------------|---------|---------|
| A | IPv4 address | www.example.com → 203.0.113.50 |
| AAAA | IPv6 address | www.example.com → 2001:db8::1 |
| CNAME | Canonical (alias) | mail.example.com → mailserver.example.com |
| MX | Mail exchange | example.com → mail.example.com (priority 10) |
| NS | Nameserver | example.com → ns1.example.com |
| SOA | Start of Authority | example.com → contact, serial, refresh |
| TXT | Text record | example.com → "v=spf1 ip4:203.0.113.0/24" (SPF) |
| PTR | Pointer (reverse) | 50.113.0.203.in-addr.arpa → www.example.com |

## Configuration (Huawei VRP)

### Recursive Resolver (Client)

```
[Router] dns resolve
[Router-dns] dns server 8.8.8.8
[Router-dns] dns server 8.8.4.4
[Router-dns] domain-name example.com

# Verify
display dns status
```

### Authoritative Server (Huawei)

```
[Router-dns] zone example.com
[Router-zone-example.com] record a www 203.0.113.50
[Router-zone-example.com] record a mail 203.0.113.100
[Router-zone-example.com] record mx 10 mail
[Router-zone-example.com] record cname alias www

# Verify
display dns zone
display dns record
```

## Query Types

### Authoritative (AA) Response
- Server has zone file for domain
- Direct answer; always accurate

### Non-authoritative (AA=0) Response
- Server cached the answer
- Answer is accurate but from cache; may be stale

### Recursive Query
- Client asks resolver to find answer
- Resolver responsible for searching

### Iterative Query
- Client asks specific server
- Server responds with what it knows or referral

## DNS Caching

- Responses cached with TTL (Time To Live)
- TTL example: 86400 (24 hours)
- Reduces queries; improves speed
- Drawback: Stale data if server changes

## Verification

```
[Router] display dns status
  DNS server:    8.8.8.8
  Domain name:   example.com
  DNS cache:     100 entries

[Router] display dns statistics
  Query requests: 1250
  Query responses: 1250
  Cache hits: 950
  Cache misses: 300
```

## Troubleshooting

```
# Test DNS resolution
[Router] ping www.example.com
Ping www.example.com (203.0.113.50)

# Query specific record
[Router] nslookup www.example.com
Address: 8.8.8.8

Name: www.example.com
Address: 203.0.113.50

# Reverse lookup
[Router] nslookup 203.0.113.50
Name: www.example.com
Address: 203.0.113.50
```

## Security Considerations

- **DNS poisoning**: Attacker injects false records
  - Mitigation: DNSSEC (cryptographic signatures)
- **DNS amplification DDoS**: Attacker uses DNS servers to flood victims
  - Mitigation: Disable recursive queries to external clients
- **Man-in-the-middle**: Intercept DNS queries and redirect
  - Mitigation: DNS over HTTPS (DoH), DNS over TLS (DoT)

## Performance

- **Recursive resolution**: 100-500ms (multi-server query)
- **Cached response**: <10ms
- **Cache hit rate**: Well-configured, 85-95% (reduces upstream load)

## Related Concepts

- [[IP Address]]
- [[DHCP]]
- [[FQDN]]
- [[TTL (Time To Live)]]
- [[DNSSEC]]
- [[Network Services]]
