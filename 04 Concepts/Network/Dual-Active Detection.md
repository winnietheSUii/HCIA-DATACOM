# Dual-Active Detection (DAD)

Mechanism to detect and resolve **split-brain** conditions in clustered/virtualized switch systems (e.g., [[CSS]]).

## Problem: Split-Brain
- Control-plane partition → both members think they are **master**
- Leads to duplicate forwarding, loops, inconsistent state

## DAD Methods
- **L3 heartbeat**: Dedicated VLAN/IP heartbeat links
- **BFD**: Fast failure detection at layer 3
- **External probe**: Third device monitors reachability

## Action on Dual-Active
- **Shut down** one system edge interfaces
- **Isolate** to prevent loops/duplication
- **Notify** operator (logs/alarms)

## Related
- [[CSS]]
- [[BFD]]
- [[STP]]
