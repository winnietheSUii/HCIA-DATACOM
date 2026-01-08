# Half-Duplex vs Full-Duplex

Modes controlling send/receive behavior on Ethernet links.

## Half-Duplex
- Cannot send and receive simultaneously
- Uses [[CSMA-CD]] to manage collisions
- Typical of hubs/legacy shared media

## Full-Duplex
- Send and receive simultaneously
- No collisions; CSMA/CD disabled
- Default on modern switch ports

## Impact
- Full-duplex increases throughput and lowers latency
- Duplex mismatch causes errors and poor performance

## Verification (Huawei VRP)
- `display interface` : Check duplex/speed
- Configure interface: `duplex full` / `duplex auto`

## Related Concepts
- [[Ethernet]]
- [[Collision Domain]]
- [[Ethernet Switch]]
