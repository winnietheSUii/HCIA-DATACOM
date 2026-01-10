# TFTP (Trivial File Transfer Protocol)

Lightweight file transfer protocol (UDP 69) used primarily for downloading configuration files, boot images, and device firmware. Simpler than FTP but with no authentication or directory navigation.

## Characteristics

- **Minimal overhead**: No authentication, directory listing, or complex commands
- **Unreliable**: UDP-based; requires simple retransmit logic
- **Small files**: Designed for config/firmware files, not large archives
- **Stateless**: Each transfer independent; no session state
- **Common use**: Network device boot images (PXE, router firmware)

## Transfer Modes

- **Octet (binary)**: Exact binary copy; files unchanged
- **NetASCII (text)**: Line-ending conversion (CRLF ↔ LF)

## How It Works

```
1. Client: RRQ (Read Request) "send me image.bin"  [UDP 69 → server:69]
2. Server: DATA block 1 (512 bytes)                [UDP:port → client]
3. Client: ACK block 1                              [UDP:port → server]
4. Server: DATA block 2                             [UDP:port → client]
5. Client: ACK block 2                              [UDP:port → server]
... (repeat until last block <512 bytes)
6. Client: ACK final block → transfer complete
```

## Configuration (Huawei VRP)

### TFTP Server

```
[Router] tftp server enable

# Enable on specific interface
[Router] interface GigabitEthernet0/0/0
[Router-GigabitEthernet0/0/0] tftp server enable
[Router-GigabitEthernet0/0/0] tftp-server root /flash

# Verify
display tftp server status
```

### TFTP Client

```
# Download configuration
[Router] tftp 203.0.113.10 get config.txt config.txt

# Upload configuration to server
[Router] tftp 203.0.113.10 put running-config running.conf

# Verify
display tftp statistics
```

## Practical Use Cases

### 1. Boot Image Transfer (Router Firmware)

```
[Router] tftp 203.0.113.10 get s5700-k9-msr.bin flash:/update.bin

# After download, apply image
[Router] system-view
[Router] boot system flash:/update.bin
[Router] reboot
```

### 2. Configuration Backup

```
# Backup running config to TFTP server
[Router] tftp 203.0.113.10 put running-config backup.conf
```

### 3. PXE Boot (Bare Metal Provisioning)

```
Client DHCP request → Receive IP + TFTP server
Client TFTP Get → bootloader file (pxelinux.0)
Client loads bootloader → boot via network
```

## Advantages

- Minimal protocol overhead
- Works on any network (no firewall complexity like FTP)
- Suitable for embedded systems with limited resources
- Commonly supported in network devices

## Disadvantages

- No authentication (security risk)
- No directory listing or file management
- UDP unreliable; requires application-level retries
- Slow for large files (512-byte blocks, many round-trips)
- Not suitable for encrypted transfer

## Verification

```
[Router] display tftp statistics
  Total requests: 150
  Successful: 148
  Failed: 2
  Average speed: 2.5 MB/s
```

## Security Considerations

- **Unencrypted**: Firmware/config in clear text on network
  - Mitigation: TFTP only on isolated management network
- **No authentication**: Any host can request files
  - Mitigation: Restrict TFTP server to trusted subnet
- **Write risk**: TFTP put allows overwriting server files
  - Mitigation: Restrict write permissions; disable TFTP write if not needed

## Modern Alternative

**SFTP (SSH File Transfer Protocol)**
- Encrypted transfer (SSH-based)
- Authentication and authorization
- Directory listing and file management
- Secure firmware/config transfer

## Related Concepts

- [[FTP]]
- [[SFTP]]
- [[UDP]]
- [[PXE (Preboot Execution Environment)]]
- [[Network Services]]
- [[Configuration Management]]
