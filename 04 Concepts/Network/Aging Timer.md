# Aging Timer

Timer used by an [[Ethernet Switch]] to remove inactive entries from the [[MAC Address Table (CAM)]].

## Behavior
- Starts/reset when frame seen with a source MAC
- Expires after configurable time (e.g., 300 seconds default)
- On expiry, entry is deleted; next traffic will be treated as unknown unicast and flooded

## Why It Matters
- Keeps table fresh and small
- Allows for host movement between ports
- Misconfigured low timers can cause frequent flooding

## Related Concepts
- [[MAC Address Table (CAM)]]
- [[Forwarding Behavior (Switch)]]
