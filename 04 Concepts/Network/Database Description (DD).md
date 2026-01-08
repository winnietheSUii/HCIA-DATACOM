# Database Description (DD)

An OSPF [[Open Shortest Path First (OSPF)|OSPF]] packet type used to synchronize [[Link-State Database (LSDB)]] between neighbors during the adjacency establishment process.

## Purpose

- Exchange summaries of Link State Advertisements (LSAs) without sending full LSA content
- Determine which LSAs are missing or out of date
- Facilitate efficient database synchronization

## When DD Packets Are Used

1. **ExStart State**: Initial DD packet exchange to establish master/slave relationship
2. **Exchange State**: Routers exchange DD packets containing LSA headers
3. Follows the **2-Way** state after bidirectional communication confirmed

## DD Packet Sequence

1. **Master Election**
   - Router with higher Router ID becomes master
   - Master initiates DD packet exchange with sequence number

2. **Initial Sync**
   - Both routers send DD packets listing LSA headers they have
   - More bit (M) indicates if more DD packets follow
   - Done bit (D) indicates database description complete

3. **Router Comparison**
   - Routers compare received DD with local LSDB
   - Identify missing or outdated LSAs
   - Transition to **Loading State** to request missing LSAs via Link State Request

## Importance

- **Efficiency**: Avoids sending full LSA content during initial sync
- **Bandwidth**: Only headers exchanged (LSA type, ID, sequence number, age)
- **Scalability**: Allows networks with large LSDBs to converge efficiently

## Related Concepts

- [[Link-State Database (LSDB)]]: Database being synchronized
- [[Open Shortest Path First (OSPF)]]
- Adjacency states: **ExStart** → **Exchange** → **Loading** → **Full**
