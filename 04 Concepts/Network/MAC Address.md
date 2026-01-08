# MAC Address

A 48-bit layer 2 address used by [[Ethernet]] and other IEEE 802 networks to identify interfaces.

## Format
- Written as 6 octets (e.g., 00:11:22:33:44:55)
- **OUI** (Organizationally Unique Identifier): First 24 bits identify vendor
- **NIC-specific**: Last 24 bits unique per interface
- **I/G bit** (LSB of first octet): 0 = unicast, 1 = multicast
- **U/L bit**: 0 = globally assigned, 1 = locally administered

## Types
- **Unicast MAC**: Single destination
- **Multicast MAC**: Group destination (e.g., 01:00:5E for IPv4 multicast)
- **Broadcast MAC**: FF:FF:FF:FF:FF:FF

## Usage in Switching
- Learned dynamically by [[Ethernet Switch]] into [[MAC Address Table (CAM)]]
- Used for forwarding decisions at layer 2
- Aging removes stale entries (default 300s typical)

## Related Concepts
- [[Ethernet Frame]]
- [[Broadcast Domain]]
- [[Collision Domain]]
