# L1: Data Communication Network Basis

## Module 1: Understanding Data Communication Fundamentals

Domain D1.1.1, D1.1.2, D1.1.3

### What is Data Communication?

Data communication refers to the exchange of digital data between two or more devices through a transmission medium such as cables, wireless, or optical fibers. A data communication network is a collection of interconnected devices (nodes) that can exchange information.

### Components of a Data Communication System

1. **Message** - The information (data) to be communicated
2. **Sender** - The device that sends the data message
3. **Receiver** - The device that receives the message
4. **Transmission Medium** - The physical path by which a message travels from sender to receiver
5. **Protocol** - A set of rules that govern data communications

### Network Types by Geographic Scope

#### LAN (Local Area Network)
- Covers a small geographic area (building, office, campus)
- High data transfer rates (100 Mbps to 10 Gbps+)
- Low latency and high reliability
- Typically owned by a single organization
- Examples: Ethernet, Wi-Fi

#### MAN (Metropolitan Area Network)
- Covers a city or large campus
- Spans tens of kilometers
- Connects multiple LANs within a metropolitan area
- Often used by ISPs to connect to customer LANs

#### WAN (Wide Area Network)
- Covers large geographic areas (countries, continents)
- Lower data rates compared to LANs
- Higher latency
- Uses technologies like MPLS, leased lines, VPN
- The Internet is the largest WAN

### Network Topologies

#### Bus Topology
- All devices connected to a single central cable (bus/backbone)
- Simple and inexpensive
- Single point of failure (the bus)
- Collision domain issues

#### Star Topology
- All devices connected to a central hub/switch
- Easy to install and manage
- Central device failure affects entire network
- Most common in modern LANs

#### Ring Topology
- Each device connected to two other devices forming a ring
- Data travels in one direction
- Failure of one device can affect the entire network
- Used in some WAN technologies

#### Mesh Topology
- Every device connected to every other device
- High redundancy and reliability
- Expensive to implement
- Used in critical network backbones

#### Tree (Hierarchical) Topology
- Combination of star and bus topologies
- Hierarchical structure with root node
- Used in large networks and campus designs

### Data Transmission Modes

#### Simplex
- One-way communication only
- Example: Television broadcast, keyboard to computer

#### Half-Duplex
- Two-way communication, but not simultaneous
- Devices take turns transmitting
- Example: Walkie-talkies

#### Full-Duplex
- Two-way simultaneous communication
- Both devices can transmit and receive at the same time
- Example: Telephone, modern Ethernet

### Network Performance Metrics

#### Bandwidth
- Maximum rate of data transfer
- Measured in bits per second (bps, Kbps, Mbps, Gbps)
- Higher bandwidth = more data capacity

#### Throughput
- Actual rate of successful data transfer
- Usually lower than bandwidth due to overhead
- Affected by network congestion, errors, processing delays

#### Latency (Delay)
- Time taken for data to travel from source to destination
- Components:
  - **Propagation delay** - Time for signal to travel through medium
  - **Transmission delay** - Time to push all bits onto the link
  - **Processing delay** - Time for router/switch to process packet
  - **Queuing delay** - Time waiting in queue at router

#### Jitter
- Variation in latency over time
- Important for real-time applications (VoIP, video)
- High jitter causes poor quality in streaming

### Network Devices

#### Hub
- Layer 1 device (Physical layer)
- Broadcasts data to all connected devices
- Creates single collision domain
- Rarely used in modern networks

#### Switch
- Layer 2 device (Data Link layer)
- Forwards frames based on MAC addresses
- Creates separate collision domains per port
- Maintains MAC address table

#### Router
- Layer 3 device (Network layer)
- Forwards packets based on IP addresses
- Connects different networks
- Performs routing decisions

#### Firewall
- Security device for traffic filtering
- Can operate at various layers
- Enforces security policies

### Transmission Media

#### Guided Media (Wired)

**Twisted Pair Cable**
- UTP (Unshielded Twisted Pair) - Most common in LANs
- STP (Shielded Twisted Pair) - Better EMI protection
- Categories: Cat5e (1Gbps), Cat6 (1-10Gbps), Cat6a, Cat7, Cat8

**Coaxial Cable**
- Better shielding than twisted pair
- Used in cable TV and older networks
- Types: Thinnet (10Base2), Thicknet (10Base5)

**Fiber Optic Cable**
- Uses light for data transmission
- Very high bandwidth, low interference
- **Single-mode** - Long distance, narrow core
- **Multi-mode** - Shorter distance, wider core

#### Unguided Media (Wireless)
- Radio waves, microwaves, infrared
- Flexibility and mobility
- Subject to interference and security concerns

### Summary

Understanding data communication fundamentals is essential for network professionals. Key concepts include:
- Network types (LAN, MAN, WAN)
- Network topologies and their characteristics
- Transmission modes (simplex, half-duplex, full-duplex)
- Performance metrics (bandwidth, throughput, latency, jitter)
- Network devices and their functions
- Transmission media options
