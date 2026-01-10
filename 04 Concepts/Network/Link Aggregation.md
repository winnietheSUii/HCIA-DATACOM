# Link Aggregation

Technique to combine multiple physical Ethernet links into one logical link to increase **bandwidth** and **reliability**.

## Standards
- **IEEE 802.3ad / 802.1AX**: Defines LACP protocol and behavior

## Modes
- **Static/Manual**: No protocol; both ends configured identically
- **Dynamic [[LACP]]**: Negotiation via LACPDU; detects mismatches and failures

## Hashing (Load Balancing)
- Per-flow hashing using **src/dst MAC**, **src/dst IP**, and/or **L4 ports**
- Ensures a single flow stays on one link (order preserved)
- Multiple flows distribute across links for aggregate throughput

## Use Cases
- Switch-to-switch uplinks
- Server NIC teaming
- Access-to-aggregation links
- Cross-device (via [[CSS]]/[[iStack]])

## Interactions
- **[[STP]]**: Trunk appears as one logical link; avoids blocking individual members
- **[[RSTP]]/[[MSTP]]**: Faster convergence; often preferred with aggregation

## Limitations
- Single flow limited by one member's bandwidth
- Hash imbalance if few large flows

## Related
- [[Eth-Trunk]]
- [[Manual Link Aggregation]]
- [[Link Aggregation Hashing]]
