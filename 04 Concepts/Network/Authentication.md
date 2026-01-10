# Authentication

First pillar of [[AAA]]: **Verify user identity** using credentials.

## Methods

### 1. Local Authentication
- **Storage**: Username/password on device
- **Advantages**: Simple; no external dependency
- **Disadvantages**: Hard to maintain at scale; no encryption; all passwords on device
- **Use**: Small networks, emergency access

Configuration:
```
[Device] local-user <username> password <password>
[Device] local-user <username> privilege level 15
[Device] line vty 0 4
[Device-line-vty0-4] authentication-mode password
```

### 2. RADIUS (Remote Authentication Dial-In User Service)
- **Storage**: External RADIUS server (e.g., Free RADIUS, Windows NPS)
- **Protocol**: UDP; port 1812 (auth), 1813 (acct)
- **Security**: Shared secret (pre-shared key) encrypts password
- **Advantages**: Centralized; easy user management; scalable
- **Disadvantages**: Network dependency; shared secret visible if intercepted

Configuration:
```
[Device] aaa
[Device-aaa] radius-scheme RADIUS_SCHEME
[Device-aaa-radius] server-ip 10.0.0.1 server-port 1812
[Device-aaa-radius] shared-key MySecret123
[Device-aaa] authentication-scheme AUTH_SCHEME
[Device-aaa-authen] authentication mode radius local
[Device] line vty 0 4
[Device-line-vty0-4] authentication-mode scheme AUTH_SCHEME
```

Fallback to local if RADIUS unreachable: `authentication mode radius local`

### 3. TACACS+ (Terminal Access Controller Access-Control System Plus)
- **Storage**: External TACACS+ server (Cisco proprietary)
- **Protocol**: TCP; port 49
- **Security**: Per-packet encryption; more secure than RADIUS
- **Advantages**: Granular control; better for command-level authorization
- **Disadvantages**: Proprietary; requires TACACS+ server

## Multi-Factor Authentication (MFA)
- Combine password + OTP (one-time password)
- Enhanced security; prevents brute-force

## Best Practices
- **Use RADIUS/TACACS+** in production (avoid local on critical devices)
- **Rotate passwords** regularly
- **Avoid weak passwords**: Enforce complexity
- **Use SSH**: Never Telnet (credentials in plain text)
- **Fallback strategy**: Local as backup if external auth fails

## Related
- [[AAA]]
- [[RADIUS]]
- [[TACACS+]]
- [[Privilege Levels]]
