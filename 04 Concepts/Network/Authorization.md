# Authorization

Second pillar of [[AAA]]: **Grant privileges** determining what a user can do.

## Privilege Levels

Huawei devices have **16 privilege levels** (0-15):

| Level | Name | Capabilities |
|-------|------|--------------|
| **0** | Guest | `ping`, `display` (basic queries) |
| **1** | Monitor | Basic `display` commands only |
| **3** | Operator | Configure basic services; restart device |
| **7** | Maintenance | Configure services; modify routing |
| **10** | Network Admin | Full access except `save` (config save) |
| **15** | Super Admin | **Full access** including `save`, device reboot |

## Configuration

**Assign privilege to user**:
```
[Device] local-user admin password 123456
[Device] local-user admin privilege level 15

[Device] local-user operator password 654321
[Device] local-user operator privilege level 3
```

**Assign privilege to commands** (command-level authorization):
```
[Device] command-authorization
[Device-cmd-auth] rule 1 command display permit
[Device-cmd-auth] rule 2 command configure terminal deny
```

**Apply scheme to VTY**:
```
[Device] line vty 0 4
[Device-line-vty0-4] authorization-mode scheme AUTH_SCHEME
```

## RADIUS-Based Authorization

RADIUS server provides privilege level in reply:
```
User: admin, RADIUS attribute: service-type = Administrative (privilege 15)
User: operator, RADIUS attribute: service-type = Callback (privilege 3)
```

Huawei translates RADIUS `service-type` to privilege levels.

## Command-Level Control

Restrict specific commands per privilege level:
```
[Device] privilege level 7 command ping deny
; Block ping at level 7 and below
```

## Best Practices

- **Principle of least privilege**: Assign minimum required level
- **Separate roles**: Create accounts for different functions (monitor, operator, admin)
- **Document policies**: Who needs what privilege and why
- **Audit periodically**: Remove stale accounts; verify appropriate levels
- **Use RADIUS server**: Centralize privilege management

## Related
- [[AAA]]
- [[Authentication]]
- [[Privilege Levels]]
