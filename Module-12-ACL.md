# Module 12: ACL Principles and Configuration

## 12.1 ACL Overview

[[ACL]] (Access Control List) is a **set of rules** on routers/switches that **permits or denies traffic** based on packet attributes.

**Purpose**:
- **Security**: Block unauthorized traffic (e.g., Telnet, specific protocols)
- **Traffic management**: Prioritize/rate-limit traffic (combined with QoS)
- **Filtering**: Reduce workload (e.g., drop invalid source IPs)
- **Access control**: Restrict remote management (VTY lines)

**How it works**:
1. Packet arrives at interface
2. Checked against [[ACL Rule]]s **sequentially** (top-to-bottom)
3. **First match wins**: Evaluation stops at first matching rule
4. **Implicit deny**: If no rule matches, packet **dropped**

**Types** (Huawei):
- **[[Basic ACL]]** (2000-2999): Filter by source IP only
- **[[Extended ACL]]** (3000-3999): Filter by source/dest IP, protocol, port
- **[[Named ACL]]**: Text-based names (recommended); supports both basic & extended

## 12.2 Basic Concepts and Working Mechanism of ACLs

### 12.2.1 Basic Concepts of ACLs

**[[ACL Rule]]** components:
- **Sequence number**: Order of evaluation (1-65535)
- **Action**: Permit or Deny
- **Match criteria**: 
  - Source IP (basic ACL)
  - Source/dest IP, protocol, port (extended ACL)

**Wildcard mask** (inverse CIDR):
- `0.0.0.0` = exact host match (e.g., 192.168.1.1)
- `0.0.0.255` = /24 network (e.g., 192.168.1.0 /24)
- `0.0.255.255` = /16 network
- `255.255.255.255` = any (`any`)

Example:
```
rule 1 permit source 192.168.1.0 0.0.0.255    ; VLAN 1 (192.168.1.0/24)
rule 2 permit source 10.0.0.1 0.0.0.0         ; Single host (10.0.0.1)
rule 3 permit source any                       ; Any source
```

**Port operators** (TCP/UDP):
- `eq <port>`: Equal (exact match)
- `gt <port>`: Greater than
- `lt <port>`: Less than
- `range <low> <high>`: Range inclusive

**TCP flags** (advanced):
- `tcp-flag syn`: Match SYN packets only (new connections)
- `tcp-flag ack`: Match ACK packets (established connections)
- Useful for filtering inbound SYN floods

### 12.2.2 Working Mechanism of ACLs

**Processing flow**:

```
Packet arrives
      ↓
Check against rule 1
      ↓
Rule 1 matches? → [Yes] → Action (permit/deny) → STOP
                 → [No] → Continue
      ↓
Check against rule 2
      ↓
Rule 2 matches? → [Yes] → Action → STOP
                 → [No] → Continue
      ↓
... (repeat for all rules)
      ↓
No rule matched → Implicit DENY (packet dropped)
```

**Key points**:
- **First match wins**: Evaluation stops immediately after match
- **Order matters**: Rearrange rules to optimize (frequent matches first)
- **Implicit deny**: Unreachable after last `permit` without explicit `permit any`

**Example with 3 rules**:
```
rule 1 permit tcp destination-port eq 80     ; Allow HTTP
rule 2 permit tcp destination-port eq 443    ; Allow HTTPS
rule 3 permit icmp                           ; Allow ICMP
; Implicit deny: All other traffic → DROPPED
```

**Traffic outcomes**:
- HTTP (port 80): Matches rule 1 → **Permitted**
- HTTPS (port 443): Skips rule 1, matches rule 2 → **Permitted**
- Ping (ICMP): Skips rules 1-2, matches rule 3 → **Permitted**
- FTP (port 21): Matches no rule → **Implicit DENY → Dropped**
- SSH (port 22): Matches no rule → **Implicit DENY → Dropped**

**Impact on performance**:
- Every packet checked against rules (CPU load)
- More rules = slower (linear search)
- **Optimization**: Place most-matched rules first

## 12.3 Basic Configurations and Applications of ACLs

### Configuration Steps

**1. Create ACL** (named, extended):
```
[Switch] acl name BLOCK_TELNET extended
```

**2. Add rules**:
```
[Switch-acl-ext-BLOCK_TELNET] rule 1 deny tcp destination-port eq 23
[Switch-acl-ext-BLOCK_TELNET] rule 2 permit ip source any destination any
```

**3. Apply to interface**:
```
[Switch] interface GigabitEthernet0/0/1
[Switch-GE0/0/1] traffic-filter inbound acl name BLOCK_TELNET
```

**4. Verify**:
```
display acl name BLOCK_TELNET
display interface GigabitEthernet0/0/1 traffic-filter
```

### Application Scenarios

**Scenario 1: Block Telnet, Allow SSH**
```
[Switch] acl name SSH_ONLY extended
[Switch-acl-ext-SSH_ONLY] rule 1 deny tcp destination-port eq 23      ; Block Telnet
[Switch-acl-ext-SSH_ONLY] rule 2 permit tcp destination-port eq 22    ; Allow SSH
[Switch-acl-ext-SSH_ONLY] rule 3 permit ip source any destination any  ; Allow all others

[Switch] interface GigabitEthernet0/0/1
[Switch-GE0/0/1] traffic-filter inbound acl name SSH_ONLY
```

**Scenario 2: Allow Web Traffic from Office Only**
```
[Switch] acl name OFFICE_WEB extended
[Switch-acl-ext-OFFICE_WEB] rule 1 permit tcp source 192.168.1.0 0.0.0.255 
    destination 10.0.0.0 0.0.255.255 destination-port range 80 443
[Switch-acl-ext-OFFICE_WEB] rule 2 deny tcp source any destination 10.0.0.0 0.0.255.255 
    destination-port range 80 443
[Switch-acl-ext-OFFICE_WEB] rule 3 permit ip source any destination any

[Switch] interface GigabitEthernet0/0/1
[Switch-GE0/0/1] traffic-filter outbound acl name OFFICE_WEB
```

**Scenario 3: Prevent ICMP Floods (Ping DoS)**
```
[Switch] acl name NO_ICMP extended
[Switch-acl-ext-NO_ICMP] rule 1 deny icmp source 0.0.0.0 255.255.255.255 
    destination 10.0.0.0 0.0.255.255
[Switch-acl-ext-NO_ICMP] rule 2 permit ip source any destination any

[Switch] interface GigabitEthernet0/0/1
[Switch-GE0/0/1] traffic-filter inbound acl name NO_ICMP
```

**Scenario 4: Block P2P Applications (High Port Ranges)**
```
[Switch] acl name NO_P2P extended
[Switch-acl-ext-NO_P2P] rule 1 deny tcp destination-port range 6000 7000
[Switch-acl-ext-NO_P2P] rule 2 deny udp destination-port range 6000 7000
[Switch-acl-ext-NO_P2P] rule 3 permit ip source any destination any

[Switch] interface GigabitEthernet0/0/1
[Switch-GE0/0/1] traffic-filter inbound acl name NO_P2P
```

### Verification & Troubleshooting

**View ACL**:
```
display acl name BLOCK_TELNET
; Shows rules, permits/denies, hit counts
```

**Check interface filter**:
```
display interface GigabitEthernet0/0/1 traffic-filter
; Shows applied ACL direction (inbound/outbound)
```

**Test with ping/telnet**:
```
PC> ping 10.0.0.1    ; Check if ICMP allowed
PC> telnet 10.0.0.1  ; Check if Telnet blocked
```

**Common issues**:
- **Implicit deny blocking intended traffic**: Add explicit permit rule or `permit any` at end
- **Rules in wrong order**: Most-matched rules should be first for performance
- **Wildcard mask errors**: Double-check; inverse of subnet mask

### Best Practices

- **Use [[Named ACL]]**: Easier than numbered; self-documenting
- **Add remarks**: Document purpose of each rule
- **Test before deployment**: Simulate traffic; verify no unintended blocking
- **Place deny rules first**: More efficient (avoids unnecessary permit checks)
- **Review periodically**: Remove obsolete rules; keep ACL concise
- **Avoid over-complexity**: Many rules impact performance; consolidate where possible

## Related Concepts

- [[Basic ACL]]
- [[Extended ACL]]
- [[Named ACL]]
- [[ACL Rule]]
- [[Traffic Filtering]]
