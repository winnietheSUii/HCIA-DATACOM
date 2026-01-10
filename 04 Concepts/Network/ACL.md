# ACL (Access Control List)

Ordered set of rules on a network device (router/switch) that **permits or denies traffic** based on packet attributes (source/destination IP, protocol, port, etc.).

## Purpose
- **Security**: Block/allow specific traffic flows
- **Traffic management**: Rate limiting, QoS prioritization
- **Filtering**: Reduce workload (e.g., block broadcast floods)

## ACL Types (Huawei)
- **[[Basic ACL]]** (2000-2999): Source IP only; simple filtering
- **[[Standard ACL]]** (≤99): Source IP; legacy
- **[[Extended ACL]]** (100-199 or 3000-3999): Source/dest IP, protocol, port; comprehensive
- **[[Named ACL]]**: Text-based names instead of numbers; flexible; recommended

## Processing
1. **Packet arrives** → Checked against ACL rules sequentially
2. **First match wins**: Stops checking once a rule matches
3. **Implicit deny**: If no rule matches, packet **dropped** (default)
4. **Direction**: Inbound (ingress filtering) or outbound (egress filtering)

## Rule Elements
- **Action**: Permit or Deny
- **Protocol**: TCP, UDP, ICMP, IP, etc.
- **Source/Dest IP**: Host or network
- **Source/Dest Port**: TCP/UDP port numbers (if applicable)
- **Sequence**: Rule numbering for insertion/deletion

## Application Points
- **Interface inbound**: Filter traffic entering interface
- **Interface outbound**: Filter traffic exiting interface
- **VTY lines**: Control Telnet/SSH access
- **RADIUS client/server**: Control AAA traffic

## Limitations
- **Processing overhead**: Checked on every packet (impacts performance)
- **Port-specific**: May not filter protocols without port numbers (ICMP, GRE)
- **Stateless**: No connection tracking (doesn't know if packet is reply or new)

## Best Practices
- **Place deny rules first**: More efficient (avoids permit checks)
- **Use named ACLs**: Easier to maintain than numbered
- **Document rules**: Add remarks explaining each rule
- **Test before applying**: Verify no unintended blocking
- **Review periodically**: Remove obsolete rules

## Related Concepts
- [[Basic ACL]]
- [[Extended ACL]]
- [[Named ACL]]
- [[ACL Rule]]
- [[Traffic Filtering]]
