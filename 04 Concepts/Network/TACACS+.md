# TACACS+ (Terminal Access Controller Access-Control System Plus)

Protocol providing [[Authentication]], [[Authorization]], and [[Accounting]]. Cisco proprietary; more granular than [[RADIUS]].

## Characteristics

- **Port**: 49 (TCP; connection-oriented)
- **Protocol**: TCP (stateful; maintains connection)
- **Security**: Per-packet encryption (more secure than RADIUS)
- **Scope**: Device/CLI access control; command-level authority
- **Use**: Network devices (routers/switches) requiring granular control

## Message Flow

**Authentication** (Login):
1. Client → Server: `START` (TCP connection; username, password)
2. Server → Client: `CONTINUE` (challenge-response if needed)
3. Client → Server: `CONTINUE` (response)
4. Server → Client: `PASS` or `FAIL`

**Authorization** (Command execution):
1. Client → Server: `AUTHOR` (user, command to execute)
2. Server → Client: `AUTHOR-PASS` (allow) or `AUTHOR-FAIL` (deny)

**Accounting**:
1. Client → Server: `ACCT` (account message; what was done)
2. Server → Client: `ACCT-REPLY` (ACK)

## Authorization Granularity

Per-**command** control (very fine-grained):

```
admin: Can run: configure, save, display
operator: Can run: display, ping
monitor: Can run: display, ping (no write)
```

## Configuration (Huawei)

**TACACS+ server setup**:
```
[Device] aaa
[Device-aaa] tacacs-scheme TACACS_SERVER
[Device-aaa-tacacs-scheme] server-ip 10.0.0.1
[Device-aaa-tacacs-scheme] server-port 49
[Device-aaa-tacacs-scheme] shared-key MySecret123
[Device-aaa-tacacs-scheme] response-timeout 5
[Device-aaa-tacacs-scheme] max-attempts 3
```

**Apply to VTY**:
```
[Device-aaa] authentication-scheme AUTH_SCHEME
[Device-aaa-authen-scheme] authentication mode tacacs+ local
[Device-aaa] authorization-scheme AUTHZ_SCHEME
[Device-aaa-authz-scheme] authorization mode tacacs+ local
[Device-aaa] accounting-scheme ACCT_SCHEME
[Device-aaa-accounting-scheme] accounting-mode tacacs+ local
[Device] line vty 0 4
[Device-line-vty0-4] authentication-mode scheme AUTH_SCHEME
[Device-line-vty0-4] authorization-mode scheme AUTHZ_SCHEME
[Device-line-vty0-4] accounting-mode scheme ACCT_SCHEME
```

## Advantages

✅ Per-packet encryption (more secure than RADIUS)
✅ Command-level authorization (granular)
✅ TCP-based (reliable; no packet loss)
✅ Separate auth/authz/accounting servers possible
✅ Better for network device management

## Disadvantages

❌ Proprietary; limited to Cisco, Huawei, Juniper, etc.
❌ Fewer open-source servers (not as common as RADIUS)
❌ More complex than RADIUS

## TACACS+ Servers

- **Cisco ISE**: Primary TACACS+ implementation
- **tac_plus**: Open-source TACACS+ server (limited)
- **Arista, Huawei, Juniper**: Native support

## RADIUS vs TACACS+ Comparison

| Feature | RADIUS | TACACS+ |
|---------|--------|---------|
| **Port** | 1812/1813 (UDP) | 49 (TCP) |
| **Encryption** | Shared-key only | Per-packet |
| **Authorization** | User/service-level | Command-level |
| **Accounting** | Basic (start/stop) | Detailed (per-command) |
| **Standards** | Open (RFC) | Proprietary (Cisco) |
| **Complexity** | Simple | Complex |
| **Use case** | ISP, WiFi, small networks | Enterprise networks, devices |

## Related
- [[AAA]]
- [[RADIUS]]
- [[Authentication]]
- [[Authorization]]
- [[Accounting]]
