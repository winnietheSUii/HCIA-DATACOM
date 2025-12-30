# L2: Network Reference Model

## Module 1: OSI Reference Model

Domain D1.2.1, D1.2.2, D1.2.3

### Introduction to Layered Architecture

Network communication is complex, involving many different functions. To manage this complexity, network architectures use a layered approach where each layer performs specific functions and provides services to the layer above it.

**Benefits of Layered Architecture:**
- Reduces complexity by dividing functions
- Standardizes interfaces between layers
- Facilitates modular engineering
- Ensures interoperability between vendors
- Accelerates evolution of technology

### OSI (Open Systems Interconnection) Model

The OSI model, developed by ISO (International Organization for Standardization), defines seven layers of network communication.

#### Layer 7: Application Layer

**Function:** Provides network services directly to end-user applications

**Responsibilities:**
- Network virtual terminal
- File transfer, access, and management
- Mail services
- Directory services

**Protocols:** HTTP, HTTPS, FTP, SMTP, DNS, DHCP, Telnet, SSH, SNMP

**PDU (Protocol Data Unit):** Data

#### Layer 6: Presentation Layer

**Function:** Data translation, encryption, and compression

**Responsibilities:**
- Data format translation (e.g., ASCII to EBCDIC)
- Data encryption/decryption
- Data compression/decompression

**Examples:** SSL/TLS, JPEG, MPEG, ASCII, EBCDIC

**PDU:** Data

#### Layer 5: Session Layer

**Function:** Manages sessions between applications

**Responsibilities:**
- Session establishment, maintenance, and termination
- Dialog control (simplex, half-duplex, full-duplex)
- Synchronization with checkpoints

**Protocols:** NetBIOS, RPC, SQL, NFS

**PDU:** Data

#### Layer 4: Transport Layer

**Function:** End-to-end communication and data delivery

**Responsibilities:**
- Segmentation and reassembly
- Connection-oriented (TCP) or connectionless (UDP) service
- Flow control
- Error detection and recovery
- Port addressing

**Protocols:** TCP, UDP, SCTP

**PDU:** Segment (TCP) / Datagram (UDP)

**Key Concepts:**
- **TCP (Transmission Control Protocol)** - Reliable, connection-oriented, flow control, error recovery
- **UDP (User Datagram Protocol)** - Unreliable, connectionless, faster, lower overhead

#### Layer 3: Network Layer

**Function:** Logical addressing and routing

**Responsibilities:**
- Logical addressing (IP addresses)
- Routing and forwarding
- Path determination
- Packet fragmentation and reassembly

**Protocols:** IP (IPv4, IPv6), ICMP, OSPF, BGP, RIP

**PDU:** Packet

**Devices:** Router, Layer 3 Switch

#### Layer 2: Data Link Layer

**Function:** Physical addressing and reliable link delivery

**Responsibilities:**
- Physical addressing (MAC addresses)
- Frame creation and error detection
- Media access control
- Flow control on the link

**Sub-layers:**
- **LLC (Logical Link Control)** - Interface with network layer
- **MAC (Media Access Control)** - Media access and addressing

**Protocols:** Ethernet, PPP, HDLC, Frame Relay, 802.11 (Wi-Fi)

**PDU:** Frame

**Devices:** Switch, Bridge, NIC

#### Layer 1: Physical Layer

**Function:** Physical transmission of raw bits

**Responsibilities:**
- Bit transmission over physical medium
- Physical topology
- Encoding and signaling
- Physical specifications (cables, connectors, voltages)

**Standards:** RS-232, V.35, Ethernet physical specifications, 802.11 physical specifications

**PDU:** Bits

**Devices:** Hub, Repeater, Cables, Connectors

### TCP/IP Model

The TCP/IP model, also known as the Internet Protocol Suite, is the practical model used in real networks.

| TCP/IP Layer | Corresponding OSI Layers |
|--------------|--------------------------|
| Application | Application, Presentation, Session |
| Transport | Transport |
| Internet | Network |
| Network Access | Data Link, Physical |

#### Application Layer (TCP/IP)
Combines OSI layers 5-7. Provides end-user services and application protocols.

#### Transport Layer (TCP/IP)
Same as OSI Layer 4. Provides end-to-end communication with TCP and UDP.

#### Internet Layer (TCP/IP)
Same as OSI Layer 3. Handles logical addressing and routing with IP.

#### Network Access Layer (TCP/IP)
Combines OSI layers 1-2. Handles physical and data link functions.

### Data Encapsulation Process

When data is sent across a network, each layer adds its own header (and sometimes trailer) to the data from the layer above.

```
Application Data → [App Header + Data]
Transport        → [TCP/UDP Header + Segment]
Network          → [IP Header + Packet]
Data Link        → [Frame Header + Frame + Frame Trailer]
Physical         → [Bits transmitted on medium]
```

### Data Decapsulation Process

When data is received, each layer removes its header and passes the data up to the next layer.

### OSI vs TCP/IP Model Comparison

| Aspect | OSI Model | TCP/IP Model |
|--------|-----------|--------------|
| Layers | 7 layers | 4 layers |
| Development | ISO standard | DARPA/Internet standard |
| Approach | Theoretical/reference | Practical/implementation |
| Protocol dependence | Protocol independent | Based on standard protocols |
| Usage | Teaching/reference | Actual implementation |

### Port Numbers

Port numbers identify applications/services at the transport layer.

**Well-Known Ports (0-1023):**
| Port | Protocol | Service |
|------|----------|---------|
| 20/21 | TCP | FTP |
| 22 | TCP | SSH |
| 23 | TCP | Telnet |
| 25 | TCP | SMTP |
| 53 | TCP/UDP | DNS |
| 67/68 | UDP | DHCP |
| 80 | TCP | HTTP |
| 110 | TCP | POP3 |
| 143 | TCP | IMAP |
| 443 | TCP | HTTPS |
| 161/162 | UDP | SNMP |

**Registered Ports (1024-49151):**
Assigned to specific applications

**Dynamic/Private Ports (49152-65535):**
Used for client-side connections

### Summary

Understanding network reference models is crucial for:
- Troubleshooting network issues layer by layer
- Understanding how different protocols interact
- Designing and implementing network solutions
- Communicating effectively with other network professionals

Key points:
- OSI model has 7 layers; TCP/IP model has 4 layers
- Each layer has specific functions and protocols
- Data encapsulation adds headers at each layer
- Understanding PDUs helps in protocol analysis
