# Port Forwarding

Configuration mechanism that directs incoming traffic on a specific port (often mapped from external IP) to a different port on an internal host. Implemented via [[NAT Server]] rules.

## Concept

Allows:
- External clients to access internal services via external IP:port
- Internal service running on non-standard port to appear on standard external port
- Single external IP to publish multiple services on different ports

## Use Cases

1. **Non-standard internal port**
   ```
   Internal web server runs on port 8080 (not 80)
   External: 203.0.113.50:80 → Internal: 192.168.1.50:8080
   ```

2. **Multiple services, single external IP**
   ```
   203.0.113.50:80   → 192.168.1.50:80   (web)
   203.0.113.50:443  → 192.168.1.50:443  (HTTPS)
   203.0.113.50:25   → 192.168.1.100:25  (mail)
   203.0.113.50:53   → 192.168.1.101:53  (DNS)
   ```

3. **Service migration**
   ```
   Old server: 192.168.1.50:8080
   New server: 10.0.0.20:8080
   
   Keep external 203.0.113.50:8080 same
   Update port forward rule (transparent to clients)
   ```

## Configuration (Huawei VRP)

### Basic Port Forwarding

```
[Router-nat] nat server protocol tcp global 203.0.113.50 80 inside 192.168.1.50 8080
```

### Multiple Ports Same Server

```
[Router-nat] nat server protocol tcp global 203.0.113.50 80 inside 192.168.1.50 80
[Router-nat] nat server protocol tcp global 203.0.113.50 443 inside 192.168.1.50 443
[Router-nat] nat server protocol tcp global 203.0.113.50 8443 inside 192.168.1.50 8443
```

### Multiple Servers, Different Ports

```
[Router-nat] nat server protocol tcp global 203.0.113.50 25 inside 192.168.1.100 25
[Router-nat] nat server protocol tcp global 203.0.113.50 110 inside 192.168.1.100:110
[Router-nat] nat server protocol tcp global 203.0.113.50 143 inside 192.168.1.100 143
```

## Verification

```
[Router] display nat server
  Protocol  Global Address         Inside Address        Status
  --------  +--------------------+--------------------  +--------
    tcp     203.0.113.50:80        192.168.1.50:8080      active
    tcp     203.0.113.50:443       192.168.1.50:443       active
    tcp     203.0.113.50:25        192.168.1.100:25       active

[Router] display nat session
# Shows active connections using port forwards
```

## Advantages

- Simple to configure per service
- Works bidirectionally (external can initiate)
- Port flexibility (remap as needed)
- Service transparency (internal IP unchanged)

## Limitations

- Manual management (one rule per port)
- Scales poorly (100+ services become unwieldy)
- DNS must resolve to external IP (not internal)
- Firewall rules must allow inbound ports

## Best Practices

1. **Document rules**: Keep mapping reference of all forwards
2. **Consistent ports**: Use standard ports when possible (80, 443, 25, etc.)
3. **Security**:
   - Only forward necessary ports
   - Consider restricting source IPs
   - Use SSL/TLS for encryption
4. **Testing**: Verify external clients can reach service
5. **Monitoring**: Track active sessions; watch for abuse

## Related Concepts

- [[NAT Server]]
- [[NAT]]
- [[Port]]
- [[NAPT (Network Address Port Translation)]]
- [[Firewall]]
