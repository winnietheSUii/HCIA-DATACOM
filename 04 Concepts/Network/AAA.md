# AAA (Authentication, Authorization, Accounting)

Framework for controlling user **access, privileges, and tracking** on network devices.

## Three Pillars

### 1. Authentication
- **Verify identity**: Who are you? (username/password, certificates)
- **Credential storage**: Local device or external server (RADIUS, TACACS+)
- **Methods**: Username/password, OTP, certificates

### 2. Authorization
- **Grant privileges**: What can you do? (read, write, execute commands)
- **Privilege levels**: 0-15 on Huawei devices
  - Level 0: Basic (e.g., ping, display)
  - Level 3: Operator (basic configuration)
  - Level 15: Administrator (full access)
- **Command restriction**: Allow/deny specific CLI commands per user

### 3. Accounting
- **Track actions**: What did you do? (log logins, commands executed, configuration changes)
- **Audit trail**: When/who/what for compliance
- **Billing**: Optional; track bandwidth usage per user

## Server Types

- **RADIUS**: Industry standard; port 1812; UDP-based; simple
- **TACACS+**: Cisco proprietary; port 49; TCP-based; granular; better security
- **Local**: Device database; no external server needed; limited to single device

## Huawei AAA Configuration

**Enable AAA**:
```
[Device] aaa
[Device-aaa] authentication-scheme <scheme-name>
[Device-aaa-authen-scheme] authentication mode local
```

**RADIUS integration**:
```
[Device-aaa] radius-scheme <scheme-name>
[Device-aaa-radius-scheme] server-ip 10.0.0.1
[Device-aaa-radius-scheme] server-port 1812
[Device-aaa-radius-scheme] shared-key password123
```

**Apply to VTY (remote access)**:
```
[Device] line vty 0 4
[Device-line-vty0-4] authentication-mode scheme <scheme-name>
[Device-line-vty0-4] authorization-mode scheme <scheme-name>
[Device-line-vty0-4] accounting-mode scheme <scheme-name>
```

## Related Concepts
- [[RADIUS]]
- [[TACACS+]]
- [[Authentication]]
- [[Authorization]]
- [[Accounting]]
- [[Privilege Levels]]
