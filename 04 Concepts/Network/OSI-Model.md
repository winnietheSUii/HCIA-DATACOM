# OSI Model (Open Systems Interconnection)

A 7-layer conceptual framework standardizing network communication functions.

## Layers (Top to Bottom)

| Layer | Name | Function | PDU | Devices |
|-------|------|----------|-----|---------|
| 7 | Application | User interface, network services | Data | - |
| 6 | Presentation | Encryption, compression, format | Data | - |
| 5 | Session | Session management, dialog control | Data | - |
| 4 | Transport | End-to-end delivery, flow control | Segment | - |
| 3 | Network | Logical addressing, routing | Packet | Router |
| 2 | Data Link | Physical addressing, framing | Frame | Switch |
| 1 | Physical | Bit transmission, media | Bits | Hub, Cable |

## Memory Tip
- **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing
- **P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way

## OSI vs TCP/IP
| OSI Layer | TCP/IP Layer |
|-----------|--------------|
| 7,6,5 | Application |
| 4 | Transport |
| 3 | Internet |
| 2,1 | Network Access |

## Related Concepts
- [[TCP-IP-Model]]
- [[Encapsulation]]
- [[Protocol]]

---
*Referenced in: [[Module-02-Network-Reference-Model#2.2 Network Reference Model and Standard Protocols]]*