# Link-State Routing Protocols

A routing protocol category where routers maintain a complete [[Network-Topology|topology database]] and flood topology information to all neighbors.

## Characteristics

- **Complete topology knowledge**: Each router knows the entire network topology
- **LSDB (Link-State Database)**: Routers maintain identical copies
- **LSA (Link State Advertisements)**: Flood topology changes to all routers
- **SPF Algorithm**: Uses Dijkstra's shortest path first to compute routes
- **Fast convergence**: Reacts quickly to topology changes
- **Higher memory/CPU**: More resource-intensive than distance-vector protocols

## Examples

- [[Open Shortest Path First (OSPF)]]
- IS-IS (Intermediate System to Intermediate System)
- EIGRP (Enhanced Interior Gateway Routing Protocol) - hybrid approach

## Advantages

- Loop prevention through topology awareness
- Fast convergence after topology changes
- Supports unequal-cost load balancing
- Better scalability in large networks

## Disadvantages

- Higher bandwidth for LSA flooding
- More CPU and memory requirements
- More complex configuration and troubleshooting
