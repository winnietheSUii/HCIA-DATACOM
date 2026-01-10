# Telnet

Remote login protocol (TCP 23) that establishes interactive terminal session on a remote host. Transmits credentials and session data in clear text.

## Characteristics

- **Interactive**: Real-time command execution
- **Terminal emulation**: Behaves like local terminal
- **Unencrypted**: All traffic including passwords in clear
- **Legacy**: Pre-dates SSH; now considered obsolete for security reasons
- **Authentication**: Username/password prompt

## How It Works

1. Client initiates TCP connection to port 23
2. Server responds with login prompt
3. User enters credentials (unencrypted)
4. Server authenticates; grants shell access
5. Commands executed; output returned to client

## Configuration (Huawei VRP)

```
# Enable Telnet server
[Router] telnet server enable

# Set VTY lines (5 default sessions)
[Router] line vty 0 4
[Router-line-vty0-4] authentication-mode aaa
[Router-line-vty0-4] protocol telnet

# AAA configuration
[Router-aaa] local-user admin password cipher Admin@123
[Router-aaa] local-user admin service-type telnet

# Verify
display telnet server status
display line vty
```

## Practical Example

```
Client $ telnet 192.168.1.1
Connected to 192.168.1.1

<Huawei Huawei MX960 Router>
Username: admin
Password: ****
[Router] display version
Huawei VRP software, Version 5.170
```

## Security Issues

- **Plaintext credentials**: Captured by packet sniffer
- **Session hijacking**: No encryption; session can be intercepted
- **Eavesdropping**: All commands/output visible

## Deployment Context

- Network management (admin access to routers/switches)
- Legacy systems without SSH support
- Isolated networks (where plaintext acceptable)

## Modern Alternative

**SSH (Secure Shell)**
- Encrypted login and session
- Public key authentication available
- Same port redirection capabilities

## Configuration: Telnet vs SSH (Huawei VRP)

```
# Telnet (legacy, not recommended)
[Router] telnet server enable

# SSH (recommended)
[Router] ssh server enable
[Router] rsa local-key-pair create

# VTY config supports both
[Router-line-vty0-4] protocol ssh telnet
```

## Best Practices

1. **Disable Telnet**: Use SSH instead
2. **If Telnet unavoidable**:
   - Restrict to trusted networks only
   - Use strong passwords
   - Monitor access logs
   - Consider VPN to encrypt transport

## Related Concepts

- [[SSH (Secure Shell)]]
- [[FTP]]
- [[Network Services]]
- [[Remote Access]]
- [[Authentication]]
