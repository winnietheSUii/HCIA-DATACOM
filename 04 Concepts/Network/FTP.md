# FTP (File Transfer Protocol)

Application-layer protocol that enables file transfer between hosts. Uses control connection (TCP 21) for commands and data connection (TCP 20 or dynamic) for file content.

## Characteristics

- **Connection model**: Separate control and data channels
- **Stateful**: Maintains session state and authentication
- **Modes**: Active (server initiates data connection) and Passive (client initiates)
- **Authentication**: Username/password required
- **Unencrypted**: Credentials and files transmitted in clear text

## Modes of Operation

### Active Mode
- Client initiates control connection (TCP 21)
- Client listens on data port; tells server via `PORT` command
- Server initiates data connection from port 20 to client's data port
- **Firewall issue**: Server-initiated inbound requires firewall exception

### Passive Mode (PASV)
- Client initiates control connection (TCP 21)
- Server listens on dynamic port; tells client via `PASV` response
- Client initiates data connection to server's port
- **Firewall-friendly**: All client-initiated

## NAT Challenge

FTP embeds IP addresses in control messages:
- `PORT 192,168,1,10,200,5` (IP 192.168.1.10, port 200*256+5)
- NAT must rewrite these; requires [[Application Layer Gateway]] (ALG)

## Configuration (Huawei VRP)

```
# Enable FTP server
[Router] ftp server enable

# Set FTP home directory
[Router] ftp server root /

# User configuration (local database)
[Router-aaa] local-user username password cipher Password123
[Router-aaa] local-user username service-type ftp

# Verify
display ftp server status
```

## Advantages

- Reliable (TCP-based)
- Efficient for bulk file transfer
- Supports resume (RESTart)
- Directory listing

## Disadvantages

- Unencrypted (security risk)
- Complex firewall configuration (active mode)
- Port exhaustion risk (separate data connection)
- Obsolete; replaced by SSH/SCP, HTTP(S)

## Modern Alternative

Use **SFTP** (SSH File Transfer Protocol) or **SCP** for encrypted transfer

## Related Concepts

- [[Telnet]]
- [[SFTP]]
- [[SCP]]
- [[Application Layer Gateway]]
- [[TCP/IP]]
