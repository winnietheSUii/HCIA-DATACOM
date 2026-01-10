# Static NAT

One-to-one permanent mapping between an internal IP and an external IP. Bidirectional; external hosts can initiate connections to the internal host.

## Characteristics

- **Fixed mapping**: One internal → one external; never changes
- **Always active**: Mapping exists whether or not traffic flows
- **Bidirectional**: Both directions automatically supported
- **Expensive**: Requires one external IP per internal IP
- **Predictable**: Same host always uses same external address

## Configuration (Huawei VRP)

```
[Router-nat] acl 2001
[Router-acl2001] rule 1 permit source 192.168.1.10 0
[Router-acl2001] quit

[Router-nat] nat static global 203.0.113.50 inside 192.168.1.10 acl 2001

# Verify
display nat static
```

## Use Cases

1. **Server Publishing**
   - Internal web server (192.168.1.50) → external (203.0.113.50)
   - External users always reach same internal address

2. **Permanent Branch Office**
   - Branch router 10.1.1.1 → HQ visible as 203.0.113.10
   - VPN or static route uses same IP always

3. **Network Device Management**
   - Internal printer (192.168.1.100) → external (203.0.113.100)
   - Admin can reach printer from outside network

## Verification

```
[Router] display nat static
  Protocol Inside IP    Inside Port Outside IP   Outside Port
  --------+-----------+-----------+-----------+-----------
     6     192.168.1.50    any    203.0.113.50   any
```

## Advantages

- Simple, predictable
- Works bidirectionally
- No session timeouts

## Disadvantages

- Address inefficient (1:1 ratio)
- Does not scale to large internal networks
- Requires one external IP per host

## Related Concepts

- [[NAT]]
- [[NAT Server]]
- [[Dynamic NAT]]
- [[IP Address]]
