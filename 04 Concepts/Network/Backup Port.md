# Backup Port

An [[RSTP]]/[[MSTP]] port role representing a backup for the same LAN segment. Rare in modern switched networks.

## Characteristics

- **Backup [[Designated Port]]**: Provides alternate on same segment
- **Discarding state**: Does not forward; prevents loops
- **Rare scenario**: Only occurs with hubs or loopback connections
- **Lower priority than alternate**: Alternate is preferred

## When Backup Port Occurs

### Scenario 1: Hub Connection
```
Switch --- Hub --- Switch (loopback)
         |     |
       Port1  Port2 (same switch)
       
Port1: Designated Port (forwarding)
Port2: Backup Port (discarding)
```

### Scenario 2: Physical Loopback
```
Switch
 |   |
 +---+ (two ports connected to each other)
 
Port1: Designated Port
Port2: Backup Port
```

### Why Rare?
- Modern networks use switches (not hubs)
- Full-duplex point-to-point links
- Loops detected during cabling (monitoring tools)

## vs. [[Alternate Port]]

| Feature | Backup Port | Alternate Port |
|---------|-------------|----------------|
| Function | Backup for same segment | Backup path to root |
| Replaces | Designated Port (on segment) | Root Port |
| Scenario | Hub, loopback | Redundant topology |
| Common? | Rare | Common |

## Selection

On same segment with multiple ports from same switch:
1. **Lower port ID** → Designated Port
2. **Higher port ID** → Backup Port

## Backup Port Activation

If designated port fails:
- Backup port transitions to designated (forwarding)
- Rapid transition in RSTP (<1 second)

## Configuration

Automatic role; cannot be manually configured. Influenced only by port ID (lower wins).

## Verification

```
display stp interface GigabitEthernet0/0/2
  - Role: Backup Port
  - State: Discarding
  - Reason: Higher port ID on same segment
```

## Related Concepts

- [[RSTP]]
- [[MSTP]]
- [[Alternate Port]]
- [[Designated Port]]
- [[Root Port]]
