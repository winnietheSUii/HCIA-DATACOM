# Data Fragmentation

The process of breaking down large [[IPv4]] packets into smaller fragments to fit within the Maximum Transmission Unit (MTU) of a network link.

## How It Works
- Network links have maximum packet size limits (e.g., 1500 bytes for Ethernet)
- If a packet exceeds this limit, the sending router fragments it
- Each fragment has its own header and is transmitted separately
- Receiving end reassembles fragments back into original packet

## Key Fields in [[IPv4 Packet Format]]
- **Flags** - Indicates if fragmentation is allowed
- **Fragment Offset** - Identifies where each fragment belongs in the original packet

## Contrast
[[Data Encapsulation]] adds headers; fragmentation breaks packets apart due to size constraints.