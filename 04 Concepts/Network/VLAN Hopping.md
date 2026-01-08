# VLAN Hopping

Security attack exploiting [[VLAN]] configuration to send traffic from one VLAN to another without authorization.

## Attack Vectors

### 1. Switch Spoofing (DTP)

- Attacker sends DTP (Dynamic Trunking Protocol) frames
- Switch auto-negotiates trunk mode
- Attacker gains access to all VLANs on trunk

**Mitigation**:
- Disable DTP: `switchport mode access` or `switchport nonegotiate`
- Manually configure trunks
- Huawei: No DTP by default (safer)

### 2. Double Tagging

```
Attacker (VLAN 10) sends double-tagged frame:
  Outer tag: VLAN 10 (native VLAN)
  Inner tag: VLAN 20 (target)

Switch 1:
  - Removes outer tag (native VLAN behavior)
  - Forwards frame with inner tag (VLAN 20)

Switch 2:
  - Sees VLAN 20 tag
  - Delivers to VLAN 20 victim
```

**Requirements**:
- Attacker in native VLAN
- Native VLAN traverses multiple switches

**Mitigation**:
- Use non-default [[Native VLAN]] (not VLAN 1)
- Prune native VLAN from trunks
- Tag all VLANs (no untagged traffic)

## Best Practices

- **Disable unused ports**: Shutdown or assign to parking VLAN
- **Access port mode**: Force edge ports to access mode
- **Prune VLANs**: Only allow necessary VLANs on trunks
- **Native VLAN**: Use uncommon ID, tag all VLANs
- **Private VLANs**: Isolate hosts within VLAN
- **Port security**: Limit MAC addresses per port
- **DHCP snooping + DAI**: Prevent ARP/DHCP attacks

## Related Concepts

- [[VLAN]]
- [[Native VLAN]]
- [[Trunk Port]]
- [[802.1Q]]
- [[VLAN Tagging]]
