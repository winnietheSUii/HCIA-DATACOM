# VLAN Tagging

Process of adding [[802.1Q]] tag to [[Ethernet Frame]]s to identify [[VLAN]] membership.

## Tag Insertion

- 4-byte tag inserted after source MAC, before EtherType/Length
- Contains VLAN ID (VID), priority (PCP), and drop eligibility (DEI)

## Tagging Behavior by Port Type

### [[Access Port]]
- **Ingress**: Untagged → switch adds tag internally
- **Egress**: Tag removed → untagged frame sent to host
- **Internal**: Frame processed with tag in switch fabric

### [[Trunk Port]]
- **Ingress**: 
  - Tagged → uses existing VID
  - Untagged → assigned to native VLAN
- **Egress**:
  - Native VLAN → sent untagged
  - Other VLANs → sent tagged

### [[Hybrid Port]]
- **Ingress**: Similar to trunk (PVID for untagged)
- **Egress**: Per-VLAN configuration (tagged or untagged)

## Frame Size Impact

- Standard frame: 64-1518 bytes
- Tagged frame: 68-1522 bytes (4 extra bytes)
- Baby giant: Some devices support 1522; others may drop
- Jumbo frames: 9000+ bytes (also need 4 extra)

## Double Tagging (Q-in-Q)

- Two 802.1Q tags: outer (provider) + inner (customer)
- Used in metro Ethernet, service provider networks
- Standard: **802.1ad**

## Security Considerations

- **VLAN hopping**: Attacker sends double-tagged frame to escape VLAN
  - Mitigation: Disable DTP, use non-default native VLAN, prune unused VLANs
- **Native VLAN mismatch**: Traffic leaks between switches
  - Mitigation: Match native VLAN on all trunks, use uncommon VLAN ID

## Related Concepts

- [[802.1Q]]
- [[VLAN]]
- [[Access Port]]
- [[Trunk Port]]
- [[Ethernet Frame]]
