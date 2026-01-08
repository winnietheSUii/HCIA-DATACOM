# TTL (Time to Live)

A field in the [[IPv4 Packet Format]] header that limits how long a packet can exist on the network.

## How It Works
- Starts with a value (typically 64 or 128)
- Each router that processes the packet decrements it by 1
- When TTL reaches 0, the packet is discarded
- Prevents packets from circulating endlessly in case of routing loops

## Purpose
Prevents routing loops and ensures packets don't traverse the network indefinitely.