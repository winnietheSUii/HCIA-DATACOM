# CSMA-CD (Carrier Sense Multiple Access with Collision Detection)

Legacy Ethernet media-access method for half-duplex shared networks.

## How It Works
1. **Carrier Sense**: Listen before transmit
2. **Multiple Access**: Many devices share medium
3. **Collision Detection**: If collision occurs, send jam signal
4. **Backoff**: Exponential backoff, then retry

## Notes
- Required minimum frame size (64 bytes) to detect collisions
- Used with hubs/bus topologies; obsolete on full-duplex switched Ethernet
- Full-duplex links disable CSMA/CD (no collisions)

## Related Concepts
- [[Ethernet]]
- [[Collision Domain]]
- [[Ethernet Frame]]
