# Module 8: VLAN Principles and Configuration

## 8.1 What Is VLAN

[[VLAN]] (Virtual LAN) is a logical [[Broadcast Domain]] created by grouping switch ports into separate layer 2 segments, regardless of physical topology.

**Problems VLANs solve:**
- Large [[Broadcast Domain]]s cause excessive broadcast traffic (ARP, DHCP, unknown unicast flooding)
- Security: All hosts in flat network can see each other's traffic
- Inflexibility: Physical topology dictates logical grouping

**VLAN benefits:**
- **Broadcast containment**: Each VLAN = isolated [[Broadcast Domain]]; broadcasts don't cross VLANs
- **Security**: Layer 2 isolation between departments/groups
- **Flexibility**: Logical grouping independent of physical location
- **Performance**: Reduced broadcast overhead improves network efficiency
- **Scalability**: Segment large networks into manageable chunks

**Range**: VLAN IDs 1-4094 (0 and 4095 reserved); VLAN 1 is default on most switches

## 8.2 VLAN Principles

### 8.2.1 VLAN Identification

VLANs use [[802.1Q]] tagging to identify frame membership:

**[[802.1Q]] frame format:**
- 4-byte tag inserted after source [[MAC Address]], before [[EtherType]]
- **TPID** (Tag Protocol ID): 0x8100 (indicates 802.1Q tag)
- **TCI** (Tag Control Information):
  - **PCP** (3 bits): QoS priority (0-7)
  - **DEI** (1 bit): Drop Eligible Indicator
  - **VID** (12 bits): VLAN ID (1-4094)

**Frame size impact:**
- Untagged: 64-1518 bytes
- Tagged: 68-1522 bytes (4 extra bytes for tag)
- Potential MTU issues if not accounted for

**Port types and tagging behavior:**

1. **[[Access Port]]**: Single VLAN, untagged
   - Hosts send/receive standard untagged frames
   - Switch adds/removes [[802.1Q]] tag internally
   - Typical: End devices (PCs, printers, servers)

2. **[[Trunk Port]]**: Multiple VLANs, tagged
   - Carries traffic for multiple VLANs using [[802.1Q]]
   - [[Native VLAN]] (default VLAN 1) sent untagged
   - Typical: Switch-to-switch, switch-to-router links

3. **[[Hybrid Port]]** (Huawei-specific): Flexible per-VLAN tagging
   - Each VLAN can be tagged or untagged independently
   - [[PVID]] determines VLAN for untagged ingress
   - Use cases: Voice + data, complex topologies

### 8.2.2 VLAN Assignment

Multiple methods for assigning frames to VLANs:

1. **[[Port-Based VLAN]]** (most common)
   - Static assignment: Port belongs to one VLAN
   - Simple, predictable, widely supported
   - Drawback: Manual reconfiguration if user moves

2. **[[MAC-Based VLAN]]**
   - Dynamic: Assign VLAN based on source [[MAC Address]]
   - User mobility: VLAN follows device regardless of port
   - Requires MAC-to-VLAN database
   - Security: Requires port security to prevent MAC spoofing

3. **[[Protocol-Based VLAN]]**
   - Assign based on [[EtherType]] (IPv4, IPv6, IPX, etc.)
   - Useful for multi-protocol networks
   - Less common in modern pure-IP environments

4. **[[Subnet-Based VLAN]]** (IP-Based)
   - Assign based on source IP address/subnet
   - Layer 3 inspection at layer 2 (adds complexity)
   - Use case: Migration scenarios

5. **[[Voice VLAN]]**
   - Special case: Single port carries data + voice VLANs
   - IP phone sends tagged frames; PC sends untagged
   - QoS priority for voice traffic

## 8.3 VLAN Applications

**Typical deployment scenarios:**

### Departmental Segmentation
```
VLAN 10: Sales (192.168.10.0/24)
VLAN 20: Engineering (192.168.20.0/24)
VLAN 30: HR (192.168.30.0/24)
VLAN 99: Management (192.168.99.0/24)
```

### Multi-Building Campus
- [[Trunk Port]]s carry multiple VLANs between buildings
- [[VLAN Pruning]]: Only allow necessary VLANs on each trunk
- Reduces broadcast traffic and improves security

### Voice and Data Convergence
- [[Voice VLAN]]: Dedicated VLAN for IP phones
- Data VLAN: Regular user traffic
- Single cable to desk; switch separates traffic

### Guest Network Isolation
- VLAN 100: Guest VLAN with restricted access
- Isolated from corporate VLANs
- Internet-only access via separate gateway

### [[Inter-VLAN Routing]]

VLANs are layer 2 constructs; hosts in different VLANs need routing to communicate:

**Method 1: Router on a Stick**
- Single physical link = [[Trunk Port]] carrying all VLANs
- Router has subinterfaces (one per VLAN) with [[802.1Q]] encapsulation
- Bottleneck: All inter-VLAN traffic through single link

**Method 2: Layer 3 Switch ([[VLAN Interface (Vlanif)]])**
- Create Vlanif (SVI) per VLAN with IP address
- Switch routes between VLANs internally at wire speed
- Modern recommended approach
- Example: Vlanif10 (192.168.10.1), Vlanif20 (192.168.20.1)

## 8.4 VLAN Configuration Examples

### 8.4.1 Basic VLAN Configuration

**Create VLANs:**
```
<Huawei> system-view
[Huawei] sysname SW1

[SW1] vlan 10
[SW1-vlan10] description Sales Department
[SW1-vlan10] quit

[SW1] vlan 20
[SW1-vlan20] description Engineering Department
[SW1-vlan20] quit
```

**Assign access ports:**
```
[SW1] interface GigabitEthernet0/0/1
[SW1-GigabitEthernet0/0/1] port link-type access
[SW1-GigabitEthernet0/0/1] port default vlan 10
[SW1-GigabitEthernet0/0/1] quit

[SW1] interface GigabitEthernet0/0/2
[SW1-GigabitEthernet0/0/2] port link-type access
[SW1-GigabitEthernet0/0/2] port default vlan 20
[SW1-GigabitEthernet0/0/2] quit
```

**Configure trunk port:**
```
[SW1] interface GigabitEthernet0/0/24
[SW1-GigabitEthernet0/0/24] port link-type trunk
[SW1-GigabitEthernet0/0/24] port trunk allow-pass vlan 10 20
[SW1-GigabitEthernet0/0/24] port trunk pvid vlan 1
[SW1-GigabitEthernet0/0/24] quit
```

**Verification:**
```
[SW1] display vlan
[SW1] display port vlan
[SW1] display mac-address
```

### 8.4.2 VLAN Configuration Examples

**Scenario**: Two switches with three VLANs; inter-VLAN routing via Layer 3 switch

```
Topology:
  SW1 (Access) --- Trunk --- SW2 (L3 Switch/Core)
  
  SW1 ports:
    Gi0/0/1-10: VLAN 10 (Sales)
    Gi0/0/11-20: VLAN 20 (Engineering)
    Gi0/0/24: Trunk to SW2
  
  SW2:
    Gi0/0/1: Trunk from SW1
    Vlanif10: 192.168.10.1/24 (gateway for VLAN 10)
    Vlanif20: 192.168.20.1/24 (gateway for VLAN 20)
```

**SW1 Configuration (Access Switch):**
```
# Create VLANs
vlan batch 10 20

# Access ports for Sales
interface range GigabitEthernet0/0/1 to GigabitEthernet0/0/10
  port link-type access
  port default vlan 10

# Access ports for Engineering
interface range GigabitEthernet0/0/11 to GigabitEthernet0/0/20
  port link-type access
  port default vlan 20

# Trunk to SW2
interface GigabitEthernet0/0/24
  port link-type trunk
  port trunk allow-pass vlan 10 20
```

**SW2 Configuration (Layer 3 Switch):**
```
# Create VLANs
vlan batch 10 20

# Trunk from SW1
interface GigabitEthernet0/0/1
  port link-type trunk
  port trunk allow-pass vlan 10 20

# VLAN interfaces for inter-VLAN routing
interface Vlanif10
  ip address 192.168.10.1 255.255.255.0
  description Gateway for Sales

interface Vlanif20
  ip address 192.168.20.1 255.255.255.0
  description Gateway for Engineering
```

**Host Configuration:**
- VLAN 10 hosts: IP 192.168.10.x/24, gateway 192.168.10.1
- VLAN 20 hosts: IP 192.168.20.x/24, gateway 192.168.20.1

**Result**: Hosts in VLAN 10 and VLAN 20 can communicate via SW2's routing

**Security Considerations:**
- Change [[Native VLAN]] from default VLAN 1 (see [[VLAN Hopping]])
- Prune unused VLANs from trunks ([[VLAN Pruning]])
- Disable unused ports or assign to parking VLAN
- Use [[Port Security Placeholder|port security]] to prevent MAC spoofing in [[MAC-Based VLAN]] scenarios
