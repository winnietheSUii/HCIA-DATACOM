# Module 13: AAA Principles and Configuration

## 13.1 AAA Overview

[[AAA]] (Authentication, Authorization, Accounting) is a security framework controlling **who can access** network devices, **what they can do**, and **tracking their actions**.

**Three pillars**:

### 1. [[Authentication]] (Authen)
- **Verify identity**: Who are you?
- **Methods**: Username/password, certificates, OTP
- **Storage**: Local device or external server ([[RADIUS]], [[TACACS+]])

### 2. [[Authorization]] (Authz)
- **Grant privileges**: What can you do?
- **Mechanism**: [[Privilege Levels]] (0-15) or command-level restrictions
- **Decision**: Allow or deny access to commands/services

### 3. [[Accounting]] (Acct)
- **Track actions**: What did you do?
- **Logging**: Who logged in/out, when, what commands executed
- **Use**: Audit trails, compliance, forensics, billing (bandwidth)

**Typical flow**:
```
User → Device → [Authentication] → Success? 
                     → [Authorization] → Permitted level? 
                         → [Accounting] → Log action
```

## 13.2 AAA Configuration

### Authentication

**Local authentication** (simplest; no external server):
```
[Device] local-user admin password 123456
[Device] local-user admin privilege level 15

[Device] aaa
[Device-aaa] authentication-scheme LOCAL_AUTH
[Device-aaa-authen-scheme] authentication mode local

[Device] line vty 0 4
[Device-line-vty0-4] authentication-mode scheme LOCAL_AUTH
```

**RADIUS authentication** (centralized; recommended):
```
[Device] aaa
[Device-aaa] radius-scheme RADIUS_SERVER
[Device-aaa-radius-scheme] server-ip 10.0.0.1 server-port 1812
[Device-aaa-radius-scheme] shared-key MySecret123

[Device-aaa] authentication-scheme RADIUS_AUTH
[Device-aaa-authen-scheme] authentication mode radius local
; Falls back to local if RADIUS unreachable

[Device] line vty 0 4
[Device-line-vty0-4] authentication-mode scheme RADIUS_AUTH
```

**[[TACACS+]] authentication** (Cisco proprietary; granular):
```
[Device] aaa
[Device-aaa] tacacs-scheme TACACS_SERVER
[Device-aaa-tacacs-scheme] server-ip 10.0.0.1 server-port 49
[Device-aaa-tacacs-scheme] shared-key MySecret123

[Device-aaa] authentication-scheme TACACS_AUTH
[Device-aaa-authen-scheme] authentication mode tacacs+ local

[Device] line vty 0 4
[Device-line-vty0-4] authentication-mode scheme TACACS_AUTH
```

### Authorization

**Privilege levels** (user-level authorization):
```
[Device] local-user admin password Admin@123
[Device] local-user admin privilege level 15
; admin: Full access (all commands)

[Device] local-user operator password Op@123
[Device] local-user operator privilege level 3
; operator: Level 3 commands (basic config)

[Device] local-user monitor password Mon@123
[Device] local-user monitor privilege level 1
; monitor: Level 1 commands (display only)
```

**RADIUS-based authorization**:
```
[Device-aaa] authorization-scheme RADIUS_AUTHZ
[Device-aaa-authz-scheme] authorization mode radius local
; RADIUS server sends service-type attribute → translates to privilege level

[Device] line vty 0 4
[Device-line-vty0-4] authorization-mode scheme RADIUS_AUTHZ
```

**TACACS+ command-level authorization** (most granular):
```
[Device-aaa] authorization-scheme TACACS_AUTHZ
[Device-aaa-authz-scheme] authorization mode tacacs+ local
; TACACS+ server decides per-command (admin can run configure, operator cannot)

[Device] line vty 0 4
[Device-line-vty0-4] authorization-mode scheme TACACS_AUTHZ
```

### Accounting

**Local accounting** (logs on device):
```
[Device] aaa
[Device-aaa] accounting-scheme LOCAL_ACCT
[Device-aaa-accounting-scheme] accounting-mode none
; Or: local (if supported)

[Device] line vty 0 4
[Device-line-vty0-4] accounting-mode scheme LOCAL_ACCT
```

**RADIUS accounting**:
```
[Device-aaa] radius-scheme RADIUS_SERVER
[Device-aaa-radius-scheme] server-ip 10.0.0.1 server-port 1813
; Note: port 1813 for accounting (1812 for auth)

[Device-aaa] accounting-scheme RADIUS_ACCT
[Device-aaa-accounting-scheme] accounting-mode radius local
; Falls back to local if RADIUS unavailable

[Device] line vty 0 4
[Device-line-vty0-4] accounting-mode scheme RADIUS_ACCT
```

**TACACS+ accounting** (most detailed):
```
[Device-aaa] tacacs-scheme TACACS_SERVER
[Device-aaa-tacacs-scheme] server-ip 10.0.0.1 server-port 49

[Device-aaa] accounting-scheme TACACS_ACCT
[Device-aaa-accounting-scheme] accounting-mode tacacs+ local

[Device] line vty 0 4
[Device-line-vty0-4] accounting-mode scheme TACACS_ACCT
```

### Complete AAA Configuration Example

**Scenario**: Remote access via SSH with RADIUS auth + accounting + local fallback

```
; Define RADIUS server
[Device] aaa
[Device-aaa] radius-scheme CORP_RADIUS
[Device-aaa-radius] server-ip 10.0.0.1 server-port 1812
[Device-aaa-radius] shared-key CorpSecret123
[Device-aaa-radius] response-timeout 5
[Device-aaa-radius] max-attempts 3

; Define authentication scheme
[Device-aaa] authentication-scheme AUTH_RADIUS
[Device-aaa-authen-scheme] authentication mode radius local

; Define authorization scheme
[Device-aaa] authorization-scheme AUTHZ_RADIUS
[Device-aaa-authz-scheme] authorization mode radius local

; Define accounting scheme
[Device-aaa] accounting-scheme ACCT_RADIUS
[Device-aaa-accounting-scheme] accounting-mode radius local

; Apply to VTY (SSH/Telnet access)
[Device] line vty 0 4
[Device-line-vty0-4] authentication-mode scheme AUTH_RADIUS
[Device-line-vty0-4] authorization-mode scheme AUTHZ_RADIUS
[Device-line-vty0-4] accounting-mode scheme ACCT_RADIUS
```

**Result**: 
- User logs in via SSH
- Password sent to RADIUS server (port 1812)
- On success, authorization checked (privilege level from RADIUS)
- All login/logout/command activity logged to RADIUS (port 1813)
- If RADIUS unreachable, falls back to local users

### Verification & Troubleshooting

**View AAA configuration**:
```
display aaa configuration
display authentication-scheme
display authorization-scheme
display accounting-scheme
```

**View local users**:
```
display local-user
```

**Check RADIUS/TACACS+ connectivity**:
```
ping 10.0.0.1  ; RADIUS server IP
```

**Common issues**:
- **Authentication fails**: Wrong shared-key; RADIUS server IP incorrect; port unreachable
- **Authorization stuck at level 0**: No privilege in RADIUS response; check server attribute
- **Accounting not logging**: Check server port (1813 for RADIUS); verify fallback mode

## Related Concepts

- [[Authentication]]
- [[Authorization]]
- [[Accounting]]
- [[RADIUS]]
- [[TACACS+]]
- [[Privilege Levels]]

## Best Practices

✅ **Use RADIUS/TACACS+** in production (centralized; avoid local passwords on every device)
✅ **Enable SSH only** (disable Telnet; credentials in plaintext)
✅ **Fallback to local** (ensures access if external server down)
✅ **Monitor RADIUS/TACACS+ health**: Timeouts, failed auth attempts
✅ **Rotate shared-keys** regularly (security best practice)
✅ **Log everything**: Enable accounting for audit/compliance
✅ **Privilege levels**: Separate roles (monitor level 1, operator level 3, admin level 15)
✅ **Document policies**: Who gets what privilege and why
