# Switching Fabric

Internal architecture of an [[Ethernet Switch]] that moves frames between ports.

## Types
- **Bus/Shared Memory**: Central memory for ingress/egress; simpler, lower cost
- **Crossbar**: Dedicated paths; high throughput, non-blocking

## Performance Metrics
- Switching capacity (bps)
- Forwarding rate (pps)
- Buffer depth (affects microbursts)

## Related Concepts
- [[Ethernet Switch]]
- [[Store-and-Forward vs Cut-Through]]
