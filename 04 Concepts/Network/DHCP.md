# DHCP (Dynamic Host Configuration Protocol)

Protocol (UDP 67 server, 68 client) that automatically assigns IP addresses and network configuration to hosts on a network.

## Purpose

- **Address automation**: No manual IP assignment
- **Scalability**: Handles hundreds/thousands of clients
- **Flexibility**: Centralized configuration (gateway, DNS, NTP)
- **Mobility**: Hosts can move between networks; get new address via DHCP

## How It Works: DORA

```
1. DISCOVER: Client broadcasts "I need an IP address"
   src=0.0.0.0:68, dst=255.255.255.255:67

2. OFFER: Server responds with available IP, lease duration
   src=server-IP:67, dst=client-broadcast:68

3. REQUEST: Client selects and confirms the offered IP
   src=0.0.0.0:68, dst=255.255.255.255:67

4. ACK: Server confirms; sends network config (gateway, DNS, etc.)
   src=server-IP:67, dst=client-IP:68
```

## Configuration (Huawei VRP)

```
[Router-dhcp] dhcp enable

[Router-dhcp] ip pool Sales-Network
[Router-dhcp-pool-Sales-Network] network 192.168.10.0 mask 255.255.255.0
[Router-dhcp-pool-Sales-Network] gateway-list 192.168.10.1
[Router-dhcp-pool-Sales-Network] dns-list 8.8.8.8 8.8.4.4
[Router-dhcp-pool-Sales-Network] lease day 7 hour 0 minute 0
[Router-dhcp-pool-Sales-Network] quit

[Router-dhcp] interface GigabitEthernet0/0/1
[Router-dhcp-if-GigabitEthernet0/0/1] dhcp select global

# Exclude addresses for static assignments
[Router-dhcp] excluded-ip-address 192.168.10.1 192.168.10.10
[Router-dhcp] excluded-ip-address 192.168.10.200 192.168.10.254

# Verify
display dhcp pool
display dhcp server statistics
```

## Key Options

| Option | Purpose | Example |
|--------|---------|---------|
| 3 | Gateway | 192.168.1.1 |
| 6 | DNS servers | 8.8.8.8, 8.8.4.4 |
| 15 | Domain name | example.com |
| 51 | Lease time | 604800 (7 days) |
| 66 | TFTP server | 192.168.1.5 |
| 67 | Boot filename | pxelinux.0 |
| 150 | TFTP server list | (Cisco) |
| 184 | Voice VLAN | (Cisco IP phones) |

## Lease Management

- **Lease time**: Duration address assigned (default 7 days)
- **Renewal (T1)**: At 50% of lease, client requests renewal
- **Rebinding (T2)**: At 87.5%, client broadcasts renewal to any server
- **Expiration**: After lease ends, IP released; new address assigned

## DHCP Relay

For multi-subnet DHCP:

```
[Router] interface GigabitEthernet0/0/2
[Router-GigabitEthernet0/0/2] dhcp select relay
[Router-GigabitEthernet0/0/2] dhcp relay server-ip 192.168.100.10

# Now, DHCP DISCOVER from subnet 192.168.20.0/24
# is relayed to server 192.168.100.10
```

## Static IP Assignment

Reserve addresses for known devices:

```
[Router-dhcp] static-bind ip-address 192.168.10.50 mac-address 00-11-22-33-44-55
```

## Verification

```
[Router] display dhcp server statistics
  Pool name: Sales-Network
  Total: 100
  Available: 45
  Used: 55
  Expired: 0

[Router] display dhcp server ip-in-use
  IP Address        MAC Address          Lease Time    Remaining Time
  +--------+-------+----+-------+--------+--------+---+----------
  192.168.10.50     00-11-22-33-44-55   7 days        3 days
  192.168.10.51     AA-BB-CC-DD-EE-FF   7 days        2 days
```

## Advantages

- Automatic configuration (zero-touch)
- Centralized management (change gateway in one place)
- Scalable (one server for thousands of clients)
- Reduces manual errors

## Disadvantages

- Requires DHCP server (dependency)
- Renewal traffic (repeated broadcasts)
- Not suitable for servers (dynamic address risky)

## Security Considerations

- **Rogue DHCP**: Attacker server offers malicious gateway/DNS
  - Mitigation: DHCP snooping, DHCP guard
- **DHCP exhaustion**: Attacker requests all IPs, denying legitimate clients
  - Mitigation: Rate limiting, IP MAC binding

## Related Concepts

- [[IP Address]]
- [[Gateway]]
- [[DNS]]
- [[TFTP]]
- [[ARP]]
- [[DHCP Snooping]]
