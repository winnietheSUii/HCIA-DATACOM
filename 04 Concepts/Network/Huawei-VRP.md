# Huawei VRP (Versatile Routing Platform)

Huawei's proprietary network operating system used on routers, switches, and firewalls.

## Features
- Unified OS across Huawei devices
- Modular architecture
- Multi-service support (routing, switching, security)
- High availability features

## File System
- Configuration files: `.cfg`, `.zip`
- System software: `.cc`
- Patch files: `.pat`

## Configuration Storage
| Type | Description |
|------|-------------|
| `current-configuration` | Running config (RAM) |
| `saved-configuration` | Startup config (Flash) |

## Basic Commands
```
display version                    # Show VRP version
display current-configuration      # Show running config
save                              # Save configuration
reboot                            # Restart device
```

## Related Concepts
- [[CLI]]

---
*Referenced in: [[Module-03-Huawei-VRP#3.1 VRP Overview]]*
