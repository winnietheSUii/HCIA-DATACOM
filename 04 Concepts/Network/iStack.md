# iStack (Huawei Stack)

Huawei technology to **stack multiple switches** into a **single logical device** for **simplified management**, **redundancy**, and **high availability**.

## Benefits
- **Single IP/CLI**: Manage stack as one device
- **Redundancy**: Master/standby roles; fast failover
- **Cross-stack aggregation**: Link aggregation across different physical members
- **Scale-out**: Add members to increase ports/capacity

## Architecture
- **Stack master**: Control-plane leader (elected by priority/MAC)
- **Standby members**: Forwarding; take over on master failure
- **Stack links**: Dedicated connections for control/data synchronization

## Failure Handling
- Master fails → standby promoted; minimal disruption

## Verification
```
display stack
```
- Shows members, roles, priorities, stack links status

## Related
- [[CSS]]
- [[Eth-Trunk]]
- [[Link Aggregation]]
