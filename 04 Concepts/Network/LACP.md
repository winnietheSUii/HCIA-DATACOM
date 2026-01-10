# LACP (Link Aggregation Control Protocol)

IEEE 802.3ad/802.1AX protocol that **dynamically forms** link aggregation between devices using LACPDU negotiation.

## Purpose
- Detects and bundles matching links
- Prevents misconfiguration (e.g., one side aggregated, other not)
- Automatically removes failed links

## Roles
- **Active**: Sends LACPDU proactively
- **Passive**: Waits; responds when LACPDU received

## Parameters
- **System priority & System ID** (MAC): Determines aggregator selection
- **Port priority**: Influences member selection
- **Timeout**: Short (fast) vs long (default) LACPDU timers

## Behavior
- Exchanges **LACPDU** frames to negotiate aggregation
- Forms **aggregators** with compatible speed/duplex/VLAN settings
- Maintains **link status**; removes failed members

## Advantages vs Manual
- Auto-detection of mismatches
- Faster, safer failover
- Better scalability and manageability

## Verification
```
display lacp
```
- Shows partner info, actor/partner states, priorities

## Related
- [[Link Aggregation]]
- [[Eth-Trunk]]
- [[Manual Link Aggregation]]
- [[Link Aggregation Hashing]]
