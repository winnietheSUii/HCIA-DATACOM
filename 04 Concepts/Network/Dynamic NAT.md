# Dynamic NAT

Many-to-few mapping where internal IPs are dynamically assigned addresses from a pool as needed. Bidirectional but requires return traffic within timeout window.

## Characteristics

- **Pool-based**: Internal IPs select from external IP pool
- **Automatic allocation**: First available IP in pool on first outbound packet
- **Connection-based**: Mapping persists for duration of session + idle timeout
- **Bidirectional**: Return traffic reaches original internal IP if within timeout
- **Efficient**: Fewer external IPs than internal IPs (typically 30-70% ratio)

## How It Works

1. **Outbound**:
   - Internal host (192.168.1.10) initiates connection
   - Router selects first available external IP from pool (e.g., 203.0.113.10)
   - Allocates to host; creates translation table entry
   - Returns traffic automatically reaches 192.168.1.10

2. **Return traffic**:
   - External reply arrives at 203.0.113.10
   - Router looks up translation table
   - Forwards to 192.168.1.10

3. **Timeout**:
   - No traffic for 300-600s (idle)
   - Entry expires; external IP returns to pool
   - New connection from same internal host may get different external IP

## Configuration (Huawei VRP)

```
[Router-nat] acl 2001
[Router-acl2001] rule 1 permit source 192.168.1.0 0.0.0.255
[Router-acl2001] quit

[Router-nat] nat address-group 1 203.0.113.50 203.0.113.59

[Router-nat] nat outbound 1 match-ace acl-2001 address-group 1

# Verify
display nat outbound
display nat session
```

## Example

```
Internal Host         External Pool          External Global IP (Dynamic)
192.168.1.10    -->   203.0.113.50-59   -->   203.0.113.50 (session 1)
192.168.1.20    -->                      -->   203.0.113.51 (session 2)
192.168.1.30    -->                      -->   203.0.113.52 (session 3)

When session 1 times out, 203.0.113.50 returns to pool.
```

## Advantages

- Address efficient (one external IP serves multiple internal IPs)
- Scalable to medium-sized networks
- Session-based; naturally expires unused mappings

## Disadvantages

- **Not bidirectional for new connections**: Cannot receive unsolicited external traffic
- **Timeout risk**: Return traffic after idle timeout fails
- **No server publishing**: Cannot map external IP to internal server permanently
- **Resource intensive**: Maintains translation table for all active sessions

## Related Concepts

- [[NAT]]
- [[NAPT (Network Address Port Translation)]]
- [[Translation Table]]
- [[Session]]
