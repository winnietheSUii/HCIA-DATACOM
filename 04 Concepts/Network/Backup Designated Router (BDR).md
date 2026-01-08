# Backup Designated Router (BDR)

A secondary router elected on [[Broadcast Multiple Access (BMA)]] networks to serve as standby for the [[Designated Router (DR)]] in [[Open Shortest Path First (OSPF)]].

## Purpose

- **Redundancy**: If DR fails, BDR immediately becomes new DR without re-election delay
- **Fast Failover**: Network continues operating without topology recalculation delay
- **Prepared State**: BDR maintains full adjacency with all routers (in Full state) so it's ready to assume DR role

## Election Criteria

Same as DR election (non-preemptive):

1. **Router Priority** (0-255, default 10)
   - Second-highest priority becomes BDR
   - Priority 0 = never elected BDR

2. **Router ID** (tiebreaker)
   - Second-highest Router ID if priorities tie

3. **Non-preemptive**
   - BDR keeps role even if higher-priority router joins
   - Only new election if BDR fails or leaves

## States and Responsibilities

### BDR State
- Forms **Full adjacency** with all routers on subnet (like DR)
- Listens passively; does not originate LSAs
- Waits to be promoted to DR

### When Promoted to DR
- Becomes active in flooding LSAs
- Originates network LSAs
- Takes full [[Designated Router (DR)]] responsibilities

### Neighbor Relationship
- Established between BDR ↔ all routers (Full state)
- Other routers send updates to both DR and BDR for redundancy

## Importance in High-Availability Networks

- **Zero-downtime failover**: BDR seamlessly replaces failed DR
- **No re-convergence delay**: Routes already in BDR's LSDB
- **Cost-effective redundancy**: Single BDR election vs. full re-election

## Configuration Example

```
interface Ethernet0/0
  ip ospf priority 50  ; Second-highest priority = likely BDR
```

## DR/BDR Failover Scenario

```
Scenario: DR fails on Ethernet network with 4 routers

Initial State:
  DR (Priority 100) - Router ID 1.1.1.1
  BDR (Priority 50) - Router ID 2.2.2.2
  DR-Other1 (Priority 10) - Router ID 3.3.3.3
  DR-Other2 (Priority 10) - Router ID 4.4.4.4

DR Fails:
  BDR immediately becomes DR (within 1 second)
  New election for BDR if another router has priority > 10
  No recalculation needed; BDR already has full LSDB
```

## Related Concepts

- [[Designated Router (DR)]]
- [[Broadcast Multiple Access (BMA)]]
- [[Open Shortest Path First (OSPF)]]
- High availability and redundancy in routing
