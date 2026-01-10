# Named ACL

ACL using **text-based names** instead of numeric IDs. Recommended modern approach.

## Characteristics
- **Naming**: Descriptive identifiers (e.g., `ALLOW_WEB`, `DENY_TELNET`)
- **Flexibility**: Easier to insert/delete rules without renumbering
- **Maintainability**: Self-documenting; improves readability
- **Types**: Can be basic or extended (named basic, named extended)

## Configuration

```
[Switch] acl name ALLOW_WEB basic
[Switch-acl-basic-ALLOW_WEB] rule 1 permit source 192.168.1.0 0.0.0.255
[Switch-acl-basic-ALLOW_WEB] rule 2 permit source 192.168.2.0 0.0.0.255

[Switch] acl name BLOCK_TELNET extended
[Switch-acl-ext-BLOCK_TELNET] rule 1 deny tcp destination-port eq 23
[Switch-acl-ext-BLOCK_TELNET] rule 2 permit ip source any destination any

[Switch] interface GigabitEthernet0/0/1
[Switch-GE0/0/1] traffic-filter inbound acl name BLOCK_TELNET
```

## Advantages vs Numbered ACLs
| Feature | Named | Numbered |
|---------|-------|----------|
| **Readability** | Self-documenting | Cryptic |
| **Editing** | Insert anywhere | Renumber all rules |
| **Maintenance** | Easy to track | Error-prone |
| **Modern standard** | ✓ Recommended | Legacy |

## Editing Rules

**Insert rule at specific sequence**:
```
[Switch-acl-ext-ALLOW_WEB] rule 5 permit tcp destination-port eq 3306
```
- If rule 5 exists, new rule inserts; existing shifts up

**Delete rule**:
```
[Switch-acl-ext-ALLOW_WEB] no rule 3
```

**View ACL**:
```
display acl name ALLOW_WEB
```

## Remarks (Documentation)

```
[Switch] acl name ALLOW_WEB extended
[Switch-acl-ext-ALLOW_WEB] rule 1 remark Allow web traffic from office
[Switch-acl-ext-ALLOW_WEB] rule 1 permit tcp source 192.168.1.0 0.0.0.255 
    destination 10.0.0.0 0.0.255.255 destination-port range 80 443
[Switch-acl-ext-ALLOW_WEB] rule 2 remark Block all others
[Switch-acl-ext-ALLOW_WEB] rule 2 deny ip source any destination any
```

## Best Practices
- Use **descriptive names**: `DENY_INVALID_SOURCE`, `ALLOW_DNS_QUERIES`
- Add **remarks** to each rule
- **Organize logically**: Deny rules first (if order matters)
- **Test before deployment**: Avoid unintended blocking

## Related
- [[ACL]]
- [[Basic ACL]]
- [[Extended ACL]]
