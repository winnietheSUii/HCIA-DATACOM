# Privilege Levels

Huawei classification of user permissions (0-15). Determines **what commands** a user can execute.

## Standard Levels

| Level | Name | Key Permissions |
|-------|------|-----------------|
| **0** | Guest | `ping`, `trace`, basic `display` |
| **1** | Monitor | Read-only `display` commands |
| **3** | Operator | Configure services; manage users; no `save` |
| **7** | Maintenance | Configure routing, interfaces, ACLs |
| **10** | Network Admin | Full config + create users |
| **15** | Super Admin | **Everything** including `save`, reboot |

## Command Classification

- **Level 0-15**: Execute in any level ≥ command level (e.g., level 7 command executable by level 7, 10, 15 users)
- **Default**: Most commands at level 15

## Configuration

**Assign privilege to user**:
```
[Device] local-user admin password 123456
[Device] local-user admin privilege level 15

[Device] local-user tech password 654321
[Device] local-user tech privilege level 7
```

**Assign privilege to command**:
```
[Device] privilege level 10 command save permit
[Device] privilege level 5 command reboot deny
```

**View user privilege**:
```
display local-user
```

## Practical Example

```
[Device] local-user admin password Admin@123
[Device] local-user admin privilege level 15
; admin: Full access (all commands)

[Device] local-user operator password Op@123
[Device] local-user operator privilege level 3
; operator: Can display, configure services
; Cannot: save, reboot, system-wide changes

[Device] local-user monitor password Mon@123
[Device] local-user monitor privilege level 1
; monitor: Read-only (display only)
; Cannot: configure anything
```

**Login behavior**:
- operator logs in → can use `display`, `configure terminal`, but not `save`
- monitor logs in → can use `display` only

## Best Practices

1. **Apply least privilege**: Don't give everyone level 15
2. **Separate roles**: 
   - Monitor (level 1): NOC/helpdesk (view-only)
   - Operator (level 3): Basic config changes
   - Tech (level 7): Advanced troubleshooting
   - Admin (level 15): Full control
3. **Document clearly**: Document who needs what level and why
4. **Audit access**: Regularly review who has level 10+ accounts
5. **Use RADIUS/TACACS+**: Centralize privilege assignment

## Related
- [[AAA]]
- [[Authentication]]
- [[Authorization]]
