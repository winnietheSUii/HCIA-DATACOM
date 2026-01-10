# Basic ACL

ACL type filtering traffic based on **source IP only** (no destination IP, protocol, or port).

## Characteristics
- **ACL numbers**: 2000-2999 (Huawei)
- **Match criteria**: Only source IP address (and masks)
- **Simple**: Fast processing; low overhead
- **Limited**: Cannot filter by destination or protocol

## Configuration

```
[Switch] acl basic ACL_NAME
[Switch-acl-basic-ACL_NAME] rule 1 permit source 192.168.1.0 0.0.0.255
[Switch-acl-basic-ACL_NAME] rule 2 deny source 10.0.0.0 0.0.255.255
[Switch-acl-basic-ACL_NAME] rule 3 permit source any
```

- `rule <sequence> <action> source <IP> <wildcard>`
- Wildcard: 0 = match, 1 = ignore (inverse of subnet mask)

## Use Cases
- Trust/untrust networks by source only
- Simple host blocking
- Rate limiting by source

## Limitations
- Cannot filter by destination IP
- Cannot filter by protocol (TCP/UDP)
- Cannot filter by port numbers

## Related
- [[ACL]]
- [[Extended ACL]]
- [[Named ACL]]
