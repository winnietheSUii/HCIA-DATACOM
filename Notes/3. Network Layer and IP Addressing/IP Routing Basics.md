# L5: IP Routing Basics

## Module 1: Understanding Routing Fundamentals

Domain D3.2.1, D3.2.2, D3.2.3

### Introduction to Routing

Routing is the process of selecting the best path for network traffic to travel from source to destination across interconnected networks. Routers use routing tables to make forwarding decisions.

### Routing Table Components

A routing table entry contains:
- **Destination Network:** The target network address
- **Subnet Mask:** Defines the network portion
- **Next-Hop Address:** IP of the next router
- **Outgoing Interface:** Interface to forward packets
- **Metric/Cost:** Value used to compare routes
- **Administrative Distance:** Route source preference

### Types of Routing

#### Static Routing
- Manually configured by administrator
- Does not adapt to network changes
- Low resource overhead
- Best for small, stable networks

#### Dynamic Routing
- Automatically discovers and maintains routes
- Adapts to network topology changes
- Higher resource overhead
- Best for large, complex networks

#### Default Routing
- Catch-all route for unknown destinations
- Typically points to the Internet gateway
- Represented as 0.0.0.0/0

### Routing Metrics

Different routing protocols use different metrics:

| Protocol | Metric |
|----------|--------|
| RIP | Hop count |
| OSPF | Cost (bandwidth-based) |
| IS-IS | Cost |
| EIGRP | Composite (bandwidth, delay, reliability, load) |
| BGP | Path attributes |

### Administrative Distance (Route Preference)

When multiple routing sources provide routes to the same destination, administrative distance determines which is preferred. **Lower values are preferred.**

| Route Source | Default AD (Huawei) |
|--------------|---------------------|
| Direct (connected) | 0 |
| Static route | 60 |
| OSPF | 10 |
| IS-IS | 15 |
| RIP | 100 |
| BGP (EBGP) | 255 |
| BGP (IBGP) | 255 |

### Longest Prefix Match

When multiple routes match a destination, the router selects the route with the longest matching prefix (most specific route).

**Example:** Destination 192.168.1.50
- Route 1: 192.168.0.0/16 → Matches
- Route 2: 192.168.1.0/24 → Matches (selected - longest match)
- Route 3: 192.168.1.0/26 → Matches (would be selected if available)

### Static Route Configuration

#### Basic Static Route
```
[Huawei] ip route-static destination-ip mask next-hop
[Huawei] ip route-static 10.0.0.0 255.0.0.0 192.168.1.2
```

#### Static Route with Outgoing Interface
```
[Huawei] ip route-static 10.0.0.0 255.0.0.0 GigabitEthernet 0/0/1
```

#### Static Route with Both
```
[Huawei] ip route-static 10.0.0.0 255.0.0.0 GigabitEthernet 0/0/1 192.168.1.2
```

#### Default Route
```
[Huawei] ip route-static 0.0.0.0 0.0.0.0 192.168.1.1
```

#### Floating Static Route (Backup Route)
```
[Huawei] ip route-static 10.0.0.0 255.0.0.0 192.168.1.2 preference 100
```

### Viewing Routing Information

```
<Huawei> display ip routing-table
<Huawei> display ip routing-table 10.0.0.0
<Huawei> display ip routing-table protocol static
<Huawei> display ip routing-table statistics
```

### Routing Table Output Interpretation

```
Destination/Mask    Proto   Pre  Cost        NextHop         Interface
10.0.0.0/8         Static  60   0           192.168.1.2     GE0/0/1
192.168.1.0/24     Direct  0    0           192.168.1.1     GE0/0/1
192.168.2.0/24     OSPF    10   10          192.168.1.3     GE0/0/2
```

**Fields:**
- **Destination/Mask:** Target network
- **Proto:** How the route was learned
- **Pre:** Administrative distance (preference)
- **Cost:** Route metric
- **NextHop:** Next router's IP
- **Interface:** Outgoing interface

### Route Summarization (Aggregation)

Combining multiple specific routes into a single summarized route:
- Reduces routing table size
- Decreases routing updates
- Improves router performance

**Example:**
```
192.168.0.0/24
192.168.1.0/24
192.168.2.0/24
192.168.3.0/24
→ Summary: 192.168.0.0/22
```

### Dynamic Routing Protocol Categories

#### Distance Vector Protocols
- Share routing tables with neighbors
- Simple but slow convergence
- Prone to routing loops
- Examples: RIP, RIPv2

#### Link-State Protocols
- Share network topology information
- Faster convergence
- More complex
- Examples: OSPF, IS-IS

#### Path Vector Protocols
- Share path information
- Used for inter-domain routing
- Example: BGP

### Interior vs Exterior Gateway Protocols

**IGP (Interior Gateway Protocol):**
- Used within an autonomous system
- Examples: OSPF, IS-IS, RIP, EIGRP

**EGP (Exterior Gateway Protocol):**
- Used between autonomous systems
- Example: BGP

### Routing Protocol Comparison

| Feature | RIP | OSPF | IS-IS | BGP |
|---------|-----|------|-------|-----|
| Type | Distance Vector | Link-State | Link-State | Path Vector |
| Metric | Hop count | Cost | Cost | Path attributes |
| Max Hops | 15 | Unlimited | Unlimited | Unlimited |
| Convergence | Slow | Fast | Fast | Slow |
| Scalability | Low | High | High | Very High |
| Complexity | Simple | Medium | Medium | Complex |

### Load Balancing

When multiple equal-cost paths exist to a destination:
- **Equal-Cost Multi-Path (ECMP):** Traffic distributed across paths
- Improves bandwidth utilization
- Provides redundancy

```
[Huawei] ip route-static 10.0.0.0 8 192.168.1.2
[Huawei] ip route-static 10.0.0.0 8 192.168.2.2
```

### Route Redistribution

Sharing routes between different routing protocols:
- Enables connectivity in multi-protocol environments
- Requires careful metric translation
- Can cause routing loops if not properly configured

### Summary

Understanding routing fundamentals is essential for network design and troubleshooting:
- Routing tables contain destination, next-hop, and metric information
- Static routes are manually configured; dynamic routes are automatically learned
- Administrative distance determines preferred route source
- Longest prefix match selects the most specific route
- Different routing protocols suit different network requirements
- Route summarization improves scalability
