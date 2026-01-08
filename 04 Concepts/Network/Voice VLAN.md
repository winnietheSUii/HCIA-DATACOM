# Voice VLAN

A special [[VLAN]] dedicated to IP phone traffic, separate from data VLAN on the same port.

## Purpose

- **QoS**: Prioritize voice traffic to prevent jitter/latency
- **Security**: Isolate voice from data traffic
- **Management**: Centralized voice network administration
- **Bandwidth**: Reserve bandwidth for voice

## How It Works

- Single [[Access Port]] carries two VLANs:
  - **Data VLAN**: Untagged (for PC)
  - **Voice VLAN**: Tagged with [[802.1Q]] (for IP phone)
- IP phone learns voice VLAN via:
  - CDP/LLDP-MED
  - DHCP option 184
  - Manual configuration

## Typical Topology

```
PC --- IP Phone --- Switch Port
       (Passthrough)

Port configuration:
  - Access mode, data VLAN 10
  - Voice VLAN 100
  - Phone sends tagged frames (VLAN 100)
  - PC sends untagged frames (VLAN 10)
```

## Configuration (Huawei VRP)

```
vlan 10
  description Data
vlan 100
  description Voice

interface GigabitEthernet0/0/1
  port link-type access
  port default vlan 10          ; Data VLAN
  voice-vlan 100 enable          ; Voice VLAN
  qos pq 4 wrr 1 2 3 5 6 7       ; Priority queue for voice
```

## Benefits

- **Single cable**: No extra cabling for IP phones
- **QoS**: Voice traffic gets priority (PCP=5 typical)
- **Security**: Separate subnets, ACLs between voice/data
- **Cost**: Reduces infrastructure complexity

## QoS Considerations

- Voice VLAN frames typically marked with:
  - **CoS** (Class of Service): PCP = 5 or 6 in 802.1Q tag
  - **DSCP**: EF (Expedited Forwarding, 46) in IP header
- Switch applies priority queuing to voice VLAN

## Security

- Separate voice from data (reduce attack surface)
- Apply ACLs to restrict voice VLAN access
- Use 802.1X for phone authentication

## Related Concepts

- [[VLAN]]
- [[Access Port]]
- [[802.1Q]]
- QoS (Quality of Service)
- LLDP-MED
