# MTU and Payload

Maximum Transmission Unit (MTU) defines the largest payload a protocol can carry without fragmentation. For standard [[Ethernet Frame]]s, payload is 46-1500 bytes.

## Ethernet MTU
- Standard payload: 1500 bytes
- Frame overhead: 18 bytes (Dst/Src MAC 12 + Type/Length 2 + FCS 4) plus 8 bytes preamble/SFD (not counted in MTU)
- **Jumbo frames**: >1500 bytes payload (commonly 9000 bytes)
- **Minimum payload**: 46 bytes (padded if shorter)

## Impact
- MTU mismatches cause fragmentation or drops
- Larger MTUs reduce per-packet overhead but must be consistent end-to-end

## Related Concepts
- [[Ethernet Frame]]
- [[Encapsulation]]
- [[CSMA-CD]]
