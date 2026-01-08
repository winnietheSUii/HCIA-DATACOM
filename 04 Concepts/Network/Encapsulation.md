# Encapsulation

The process of adding protocol-specific headers (and sometimes trailers) to data as it moves down the OSI/TCP-IP layers.

## Process
```
Application Data
    ↓ + L4 Header → Segment
    ↓ + L3 Header → Packet
    ↓ + L2 Header/Trailer → Frame
    ↓ + L1 → Bits
```

## PDU (Protocol Data Unit) Names
| Layer | PDU Name |
|-------|----------|
| Application | Data |
| Transport | Segment (TCP) / Datagram (UDP) |
| Network | Packet |
| Data Link | Frame |
| Physical | Bits |

## Decapsulation
The reverse process at the receiving end, removing headers as data moves up the layers.

## Related Concepts
- [[OSI-Model]]
- [[TCP-IP-Model]]

---
*Referenced in: [[Module-02-Network-Reference-Model#2.3 Data Communication Process]]*
