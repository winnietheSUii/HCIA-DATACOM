# RADIUS (Remote Authentication Dial-In User Service)

Protocol providing [[Authentication]], [[Authorization]], and [[Accounting]] (AAA) services. Industry standard; widely supported.

## Characteristics

- **Port**: 1812 (authentication), 1813 (accounting)
- **Protocol**: UDP (stateless; no connection)
- **Security**: Shared secret (pre-shared key); MD5 hashing
- **Scope**: User/network access control
- **Use**: ISPs, enterprise networks, WiFi authentication

## Message Flow

**Authentication** (Login):
1. Client → RADIUS server: `Access-Request` (username, password encrypted with shared-key)
2. Server → Client: `Access-Accept` (auth success) or `Access-Reject` (auth fail)

**Accounting**:
1. Client → Server: `Accounting-Request` (Start event on login)
2. Server → Client: `Accounting-Response` (ACK)
3. Later → Server: `Accounting-Request` (Stop event on logout)

## Attributes

RADIUS carries attributes in requests/responses:

| Attribute | Purpose |
|-----------|---------|
| **User-Name** | Login username |
| **User-Password** | Password (encrypted with shared-key) |
| **Service-Type** | Authorization level (e.g., Administrative = privilege 15) |
| **Reply-Message** | Custom message from server |
| **Acct-Status-Type** | Start/Stop/Interim (accounting) |
| **Acct-Input-Octets** | Bytes received |
| **Acct-Output-Octets** | Bytes sent |

## Configuration (Huawei)

**RADIUS server setup**:
```
[Device] aaa
[Device-aaa] radius-scheme RADIUS_SERVER
[Device-aaa-radius-scheme] server-ip 10.0.0.1
[Device-aaa-radius-scheme] server-port 1812
[Device-aaa-radius-scheme] shared-key MySecret123
[Device-aaa-radius-scheme] response-timeout 5
[Device-aaa-radius-scheme] max-attempts 3
```

**Apply to VTY**:
```
[Device-aaa] authentication-scheme AUTH_SCHEME
[Device-aaa-authen-scheme] authentication mode radius local
[Device-aaa] accounting-scheme ACCT_SCHEME
[Device-aaa-accounting-scheme] accounting-mode radius local
[Device] line vty 0 4
[Device-line-vty0-4] authentication-mode scheme AUTH_SCHEME
[Device-line-vty0-4] accounting-mode scheme ACCT_SCHEME
```

## Advantages

✅ Open standard; multi-vendor support
✅ Centralized user management
✅ Built-in accounting
✅ Wide RADIUS server availability (Free RADIUS, Windows NPS, Cisco ISE)

## Disadvantages

❌ Shared secret transmitted in plaintext (on network)
❌ UDP-based; connectionless; potential packet loss
❌ Less granular than TACACS+ (no command-level control)

## Common RADIUS Servers

- **Free RADIUS**: Open-source; Linux/Unix
- **Windows NPS**: Built-in with Windows Server
- **Cisco ISE**: Enterprise; highly integrated
- **Okta, Auth0**: Cloud-based; modern apps

## Related
- [[AAA]]
- [[TACACS+]]
- [[Authentication]]
- [[Authorization]]
- [[Accounting]]
