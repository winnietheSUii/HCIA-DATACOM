# NTP (Network Time Protocol)

Protocol (UDP 123) that synchronizes clocks across network devices to a common time source. Enables accurate logging, authentication, and coordination.

## Purpose

- **Time synchronization**: All devices within milliseconds of each other
- **Logging accuracy**: Events timestamped consistently
- **Certificate validation**: SSL/TLS certificates require accurate time
- **Security**: Kerberos, other protocols depend on time sync
- **Audit trail**: Legal compliance (financial, medical records)

## How It Works: Timestamp Exchanges

```
Client sends:          Transmit timestamp (T1)
Server responds with:  
  - Originate: T1 (client's send time)
  - Receive: T2 (server received at)
  - Transmit: T3 (server sent at)
Client calculates:
  - Round trip: T4 - T1 (T4 = local receive time)
  - Server delay: (T2 - T1) + (T4 - T3)
  - Offset: [(T2-T1) + (T3-T4)] / 2

Adjust local clock by offset
```

## Hierarchical Structure: Stratum Levels

```
Stratum 0: Atomic clock (GPS, radio)
Stratum 1: Servers connected directly to atomic clock (highly accurate)
Stratum 2: Servers synced from Stratum 1 (good accuracy)
Stratum 3: Servers synced from Stratum 2 (acceptable accuracy)
...
Stratum 16: Unsynchronized; invalid
```

## Configuration (Huawei VRP)

### As NTP Client

```
[Router] ntp-service enable
[Router] ntp-service server 203.0.113.10 source GigabitEthernet0/0/0
[Router] ntp-service server 203.0.113.11 source GigabitEthernet0/0/0 prefer

# Verify
display ntp-service status
display ntp-service session
```

### As NTP Server

```
[Router] ntp-service enable
[Router] ntp-service server listen ip 192.168.1.1
[Router] ntp-service server listen port 123
[Router] ntp-service enable-authentication

# Verify
display ntp-service statistics
```

## Typical Deployment

```
Internet NTP Pool (stratum 1-2)
         ↓
    ISP NTP Server (stratum 2)
         ↓
Corporate NTP Server (stratum 3)
         ↓
Network devices (routers, switches, servers)
```

## Verification

```
[Router] display ntp-service status
  NTP enabled
  Current time:  2025-01-10 14:23:45
  Sync status:   Synchronized (stratum 3)
  Last sync:     10 seconds ago
  Time source:   203.0.113.10

[Router] display ntp-service session
  Address            Status        Last Receive    Poll Interval
  +-----+-----------+-------+------+------+-------+-------
  203.0.113.10       synchronized  8s              64s
  203.0.113.11       candidate     120s            128s
```

## Accuracy Characteristics

- **LAN**: ±10ms (microseconds possible with high-end gear)
- **Internet**: ±100ms
- **Over public internet**: ±500ms typical

## Authentication

Prevent NTP spoofing (attacker sends wrong time):

```
[Router] ntp-service authentication enable
[Router] ntp-service authentication-key 1 password NtpSecret123
[Router] ntp-service server 203.0.113.10 authentication key 1
```

## Security Considerations

- **DDoS amplification**: NTP requests amplified in attacks
  - Mitigation: Restrict NTP queries to internal only
- **Time jumping**: Sudden time change breaks certificate validation, logs
  - Mitigation: Use NTP carefully in distributed systems; monitor changes

## Testing

```
# Check time difference
[Router] date
2025-01-10 14:25:30

[Router] ntp-service time adjustment
  Offset: +2.5ms (synchronized within tolerance)
```

## Best Practices

1. **Multiple servers**: At least 3 NTP sources for redundancy
2. **Prefer lower stratum**: Set `prefer` on most accurate server
3. **VRF isolation**: If using VRFs, NTP respects VRF routing
4. **Monitor sync**: Alert if stratum > 10 or sync lost
5. **Restrict access**: Allow NTP queries only from trusted networks

## Related Concepts

- [[Time Synchronization]]
- [[Stratum]]
- [[Clock Accuracy]]
- [[Security]]
- [[Network Services]]
