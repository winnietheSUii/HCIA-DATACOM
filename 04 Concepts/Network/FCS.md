# FCS (Frame Check Sequence)

A 4-byte CRC-32 trailer in an [[Ethernet Frame]] used for error detection.

## Purpose
- Detects bit errors in the received frame
- Calculated over Destination MAC through payload
- Dropped if FCS check fails (on store-and-forward switches)

## Notes
- Not corrected at layer 2; relies on higher-layer retransmission
- Not forwarded across most tunnels (recomputed per-hop)

## Related Concepts
- [[Ethernet Frame]]
- [[Store-and-Forward vs Cut-Through]]
