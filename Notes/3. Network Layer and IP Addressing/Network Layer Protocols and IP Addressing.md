# L4: Network Layer Protocols and IP Addressing

## Module 1: Understanding IP Addressing

Domain D3.1.1, D3.1.2, D3.1.3

### Introduction to IP Addressing

The Internet Protocol (IP) provides logical addressing for devices on a network. IP addresses are used to identify the source and destination of packets and enable routing across networks.

### IPv4 Address Structure

An IPv4 address is a 32-bit number, typically written in dotted decimal notation.

**Format:** `xxx.xxx.xxx.xxx` where each xxx is a decimal number from 0 to 255

**Example:** `192.168.1.100`

**Binary Representation:**
```
192.168.1.100
= 11000000.10101000.00000001.01100100
```

### IPv4 Address Classes

| Class | First Octet Range | Default Mask | Network/Host Bits | Purpose |
|-------|-------------------|--------------|-------------------|---------|
| A | 1-126 | 255.0.0.0 (/8) | 8/24 | Large networks |
| B | 128-191 | 255.255.0.0 (/16) | 16/16 | Medium networks |
| C | 192-223 | 255.255.255.0 (/24) | 24/8 | Small networks |
| D | 224-239 | N/A | N/A | Multicast |
| E | 240-255 | N/A | N/A | Experimental |

**Note:** 127.x.x.x is reserved for loopback (localhost)

### Private IP Address Ranges (RFC 1918)

| Class | Range | CIDR |
|-------|-------|------|
| A | 10.0.0.0 - 10.255.255.255 | 10.0.0.0/8 |
| B | 172.16.0.0 - 172.31.255.255 | 172.16.0.0/12 |
| C | 192.168.0.0 - 192.168.255.255 | 192.168.0.0/16 |

### Special IP Addresses

| Address | Purpose |
|---------|---------|
| 0.0.0.0 | Default route / unspecified |
| 127.0.0.1 | Loopback address |
| 255.255.255.255 | Limited broadcast |
| Network.255 | Directed broadcast |
| Network.0 | Network address |
| 169.254.x.x | Link-local (APIPA) |

### Subnet Mask

A subnet mask determines which portion of an IP address is the network portion and which is the host portion.

**Example:**
```
IP Address:    192.168.1.100
Subnet Mask:   255.255.255.0
Network:       192.168.1.0
Host:          .100
Broadcast:     192.168.1.255
```

### CIDR Notation

CIDR (Classless Inter-Domain Routing) uses a suffix to indicate the number of network bits.

| CIDR | Subnet Mask | Hosts |
|------|-------------|-------|
| /8 | 255.0.0.0 | 16,777,214 |
| /16 | 255.255.0.0 | 65,534 |
| /24 | 255.255.255.0 | 254 |
| /25 | 255.255.255.128 | 126 |
| /26 | 255.255.255.192 | 62 |
| /27 | 255.255.255.224 | 30 |
| /28 | 255.255.255.240 | 14 |
| /29 | 255.255.255.248 | 6 |
| /30 | 255.255.255.252 | 2 |
| /31 | 255.255.255.254 | 2 (point-to-point) |
| /32 | 255.255.255.255 | 1 (host route) |

### Subnetting

Subnetting divides a network into smaller subnetworks for:
- Efficient IP address utilization
- Network organization
- Security and traffic control
- Broadcast domain reduction

#### Subnetting Calculation

**Formula:**
- Number of subnets = 2^n (n = borrowed bits)
- Hosts per subnet = 2^h - 2 (h = host bits)

**Example:** Subnet 192.168.1.0/24 into 4 subnets
- Need 2 bits for 4 subnets (2^2 = 4)
- New mask: /26 (255.255.255.192)
- Hosts per subnet: 2^6 - 2 = 62

**Resulting Subnets:**
```
192.168.1.0/26   (192.168.1.1 - 192.168.1.62)
192.168.1.64/26  (192.168.1.65 - 192.168.1.126)
192.168.1.128/26 (192.168.1.129 - 192.168.1.190)
192.168.1.192/26 (192.168.1.193 - 192.168.1.254)
```

### VLSM (Variable Length Subnet Masking)

VLSM allows different subnet masks within the same network, enabling efficient address allocation based on actual requirements.

### IP Packet Structure

| Field | Size | Description |
|-------|------|-------------|
| Version | 4 bits | IP version (4) |
| IHL | 4 bits | Header length |
| ToS/DSCP | 8 bits | Type of service |
| Total Length | 16 bits | Packet size |
| Identification | 16 bits | Fragment ID |
| Flags | 3 bits | Fragmentation flags |
| Fragment Offset | 13 bits | Fragment position |
| TTL | 8 bits | Time to live |
| Protocol | 8 bits | Upper layer protocol |
| Header Checksum | 16 bits | Error checking |
| Source IP | 32 bits | Sender address |
| Destination IP | 32 bits | Receiver address |
| Options | Variable | Optional fields |
| Data | Variable | Payload |

### ICMP (Internet Control Message Protocol)

ICMP is used for error reporting and diagnostic functions.

**Common ICMP Message Types:**

| Type | Name | Description |
|------|------|-------------|
| 0 | Echo Reply | Ping response |
| 3 | Destination Unreachable | Cannot reach destination |
| 5 | Redirect | Better route available |
| 8 | Echo Request | Ping request |
| 11 | Time Exceeded | TTL expired |

### ARP (Address Resolution Protocol)

ARP resolves IP addresses to MAC addresses on a local network.

**ARP Process:**
1. Host checks ARP cache for MAC
2. If not found, sends ARP Request (broadcast)
3. Target host responds with ARP Reply (unicast)
4. Requesting host caches the mapping

**ARP Commands (Huawei VRP):**
```
<Huawei> display arp all
<Huawei> arp static 192.168.1.100 0001-0002-0003
<Huawei> reset arp all
```

### IP Configuration on Huawei Devices

#### Interface IP Configuration
```
[Huawei] interface GigabitEthernet 0/0/1
[Huawei-GigabitEthernet0/0/1] ip address 192.168.1.1 255.255.255.0
[Huawei-GigabitEthernet0/0/1] ip address 192.168.1.2 255.255.255.0 sub
```

#### Secondary IP Address
An interface can have multiple IP addresses (one primary, multiple secondary).

#### View IP Configuration
```
<Huawei> display ip interface brief
<Huawei> display ip interface GigabitEthernet 0/0/1
```

### Summary

Understanding IP addressing is fundamental for network engineering:
- IPv4 uses 32-bit addresses in dotted decimal notation
- Address classes define default network sizes
- Private addresses are used within organizations
- Subnetting divides networks efficiently
- CIDR provides flexible address allocation
- ARP maps IP addresses to MAC addresses
- ICMP provides diagnostic capabilities
