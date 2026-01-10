# Easy IP

Simplified [[NAPT]] where the external address is the interface IP itself, without explicit pool configuration. Automatically performs port translation on a single outbound interface.

## Characteristics

- **Interface-based**: Uses interface IP as external address; no separate pool
- **Automatic**: Single command; minimal configuration overhead
- **NAPT equivalent**: Multiplexes connections via ports like NAPT
- **Dynamic source port**: Allocates ports from dynamic range automatically
- **Common**: ISP gateways, branch offices, home routers

## Configuration (Huawei VRP)

```
[Router] acl 2001
[Router-acl2001] rule 1 permit source 192.168.1.0 0.0.0.255
[Router-acl2001] quit

[Router] nat outbound 1 match-ace acl-2001 interface GigabitEthernet0/0/0 overload

# Verify
display nat outbound
display nat session all
```

## Behavior

```
Internal: 192.168.1.10:54321 → external-host:80
Interface GigabitEthernet0/0/0: IP 203.0.113.1

Translation:
Src-IP: 192.168.1.10 → 203.0.113.1 (interface IP)
Src-port: 54321 → dynamic (e.g., 12000)

External packet: 203.0.113.1:12000 → external-host:80
```

## Advantages

- **Simplest config**: One command vs. pool + rule setup
- **Scalable**: Handles thousands of internal hosts
- **Interface-agnostic**: Works with static or dynamic IP (DHCP, PPP)
- **No management**: No manual pool allocation

## Disadvantages

- **Interface IP required**: Changes if IP changes (DHCP renewal)
- **Only outbound**: No external-to-internal connection initiation
- **Single external address**: Limits return traffic to that IP
- **Port exhaustion risk**: If many long-lived connections from same internal IP, port space limited

## Use Cases

1. **Branch office**:
   - Internal network 10.0.0.0/24
   - Outbound to HQ/internet via Easy IP on branch interface
   - No permanent server publishing

2. **Home network**:
   - All internal devices behind router's interface IP
   - Typical DSL/cable setup

3. **Mobile gateway**:
   - Internal mobile devices → external carrier network
   - Single point of NAT translation

## Comparison: Easy IP vs NAPT vs Dynamic NAT

| Feature | Easy IP | NAPT Pool | Dynamic NAT |
|---------|---------|-----------|-------------|
| Config | Simplest | Complex | Medium |
| Bidirectional | No | No | Yes |
| Scalability | High | High | Medium |
| Interface-dependent | Yes (auto-uses IP) | No (explicit pool) | No (explicit pool) |
| Common | Yes | Enterprise | Enterprise |

## Related Concepts

- [[NAPT (Network Address Port Translation)]]
- [[NAT]]
- [[Port]]
- [[Interface]]
