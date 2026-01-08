# ICMP Error Report

The mechanism by which network devices communicate errors back to the originating device.

## Structure of Error Reports

### What's Included
- **Original packet header** - The IP header of the problematic packet
- **First 8 bytes of payload** - Data following the problematic packet
- **Error code/type** - Specific error classification
- **Additional parameter** - Context-specific information

## Process

1. **Problem detected** - Router/host encounters packet issue
2. **Error message generated** - ICMP error packet created
3. **Error message sent** - Directed to original packet source
4. **Source processes error** - Takes corrective action

## Error Codes

### Format
- **Type field** - Identifies error category (3=Destination Unreachable, 11=Time Exceeded, etc.)
- **Code field** - Specifies exact error (within that category)

### Example
- Type 3, Code 1 = Host Unreachable
- Type 11, Code 0 = TTL Exceeded in Transit

## Important Note
- **ICMP errors only go to source** - Never generate errors for error messages (prevents loops)
- **Packet discarded** - Original problematic packet is not forwarded
- **Immediate notification** - Sent as soon as error is detected