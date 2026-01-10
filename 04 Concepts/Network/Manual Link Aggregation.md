# Manual Link Aggregation (Static)

Static bundling of ports without a control protocol. Both ends must be **manually configured** identically.

## Characteristics
- No LACPDU exchange
- Simpler; minimal overhead
- Requires strict config consistency on both ends

## Risks
- **Mismatched configs** → blackholing or loops
- Harder troubleshooting than [[LACP]]

## Use Cases
- Simple point-to-point links
- Environments disallowing dynamic protocols

## Verification
```
display eth-trunk
```
- Confirms trunk mode = manual/static; member states

## Related
- [[Eth-Trunk]]
- [[Link Aggregation]]
- [[LACP]]
