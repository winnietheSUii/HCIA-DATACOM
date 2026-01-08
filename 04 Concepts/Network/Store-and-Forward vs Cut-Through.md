# Store-and-Forward vs Cut-Through

Switching methods used by Ethernet switches to handle frames.

## Store-and-Forward
- Receives entire frame before forwarding
- Checks [[Ethernet Frame|FCS]]; drops on error
- Adds latency equal to frame serialization + processing
- Default on most enterprise switches (including Huawei)

## Cut-Through
- Begins forwarding after reading Destination MAC
- Lower latency, but forwards errored frames (no FCS check)
- Variants: Fast-forward, Fragment-free (waits 64 bytes)

## When to Use
- Store-and-forward: Reliability, mixed speeds, error isolation
- Cut-through: Low-latency environments (HFT, some DC fabrics)

## Related Concepts
- [[Ethernet Switch]]
- [[Ethernet Frame]]
