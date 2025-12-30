# L3: Huawei VRP Basics

## Module 1: Understanding Huawei VRP

Domain D2.1.1, D2.1.2, D2.1.3

### Introduction to VRP

**VRP (Versatile Routing Platform)** is Huawei's proprietary network operating system that runs on Huawei network devices including routers and switches. VRP provides a unified platform for network configuration and management.

### VRP Features

- **Unified Platform:** Same OS across different hardware platforms
- **Modular Design:** Separate functional modules for routing, switching, security
- **High Reliability:** Support for various redundancy mechanisms
- **Rich Protocols:** Comprehensive protocol support
- **Flexible Management:** CLI, Web, SNMP, and NETCONF support

### VRP File System

#### File Types

| File Type | Extension | Description |
|-----------|-----------|-------------|
| System Software | .cc | Main VRP operating system |
| Configuration File | .cfg, .zip | Device configuration |
| PAT File | .pat | Patch files |
| License File | .dat | Feature license files |

#### Storage Devices
- **Flash:** Onboard flash memory
- **SD Card:** External storage
- **USB:** USB flash drives
- **NVRAM:** Non-volatile RAM for configuration 

### VRP User Interfaces

#### Console Port
- Direct physical connection
- RS-232 serial interface
- Used for initial configuration
- Settings: 9600 baud, 8 data bits, no parity, 1 stop bit

#### VTY (Virtual Type Terminal)
- Remote access via Telnet or SSH
- Multiple simultaneous sessions supported
- Configured with VTY line settings

#### Web Interface
- HTTP/HTTPS-based management
- Graphical user interface
- Suitable for basic configuration

### Command Line Interface (CLI)

#### Command Views (Modes)

**User View**
```
<Huawei>
```
- First view after login
- Basic commands: display, ping, tracert
- Limited configuration capability

**System View**
```
<Huawei> system-view
[Huawei]
```
- Global configuration mode
- Access with `system-view` command
- Configure system-wide parameters

**Interface View**
```
[Huawei] interface GigabitEthernet 0/0/1
[Huawei-GigabitEthernet0/0/1]
```
- Interface-specific configuration
- Enter with `interface <type> <number>`

**Protocol View**
```
[Huawei] ospf 1
[Huawei-ospf-1]
```
- Protocol-specific configuration
- Various protocol views available

#### Basic CLI Commands

**Navigation:**
- `quit` - Return to previous view
- `return` - Return to user view
- `Ctrl+Z` - Return to user view immediately

**Help:**
- `?` - Display available commands
- `<command> ?` - Display command parameters
- `Tab` - Auto-complete commands

**History:**
- `display history-command` - View command history
- Up/Down arrows - Navigate history

### Basic Configuration Commands

#### System Name
```
[Huawei] sysname Router1
[Router1]
```

#### Clock Configuration
```
<Huawei> clock datetime 12:00:00 2024-01-15
<Huawei> clock timezone BJ add 08:00:00
```

#### Header/Banner Configuration
```
[Huawei] header login information "Welcome to the network"
[Huawei] header shell information "Authorized access only"
```

### User Authentication Configuration

#### Console Authentication
```
[Huawei] user-interface console 0
[Huawei-ui-console0] authentication-mode password
[Huawei-ui-console0] set authentication password cipher Huawei@123
```

#### VTY Authentication
```
[Huawei] user-interface vty 0 4
[Huawei-ui-vty0-4] authentication-mode aaa
[Huawei-ui-vty0-4] protocol inbound ssh
```

#### AAA Configuration
```
[Huawei] aaa
[Huawei-aaa] local-user admin password cipher Admin@123
[Huawei-aaa] local-user admin service-type terminal ssh
[Huawei-aaa] local-user admin privilege level 15
```

### User Privilege Levels

| Level | Description | Access |
|-------|-------------|--------|
| 0 | Visit level | ping, tracert, telnet |
| 1 | Monitor level | display commands |
| 2 | Configure level | Most configuration |
| 3-15 | Management level | All commands |

### Configuration File Management

#### View Current Configuration
```
<Huawei> display current-configuration
```

#### View Saved Configuration
```
<Huawei> display saved-configuration
```

#### Save Configuration
```
<Huawei> save
```

#### Compare Configurations
```
<Huawei> compare configuration
```

#### Reset Configuration
```
<Huawei> reset saved-configuration
```

### File Management Commands

#### Directory Operations
```
<Huawei> dir flash:/
<Huawei> cd flash:/
<Huawei> pwd
<Huawei> mkdir test
<Huawei> rmdir test
```

#### File Operations
```
<Huawei> copy source-file destination-file
<Huawei> move source-file destination-file
<Huawei> delete filename
<Huawei> undelete filename
<Huawei> reset recycle-bin
```

### System Upgrade

#### TFTP/FTP File Transfer
```
<Huawei> tftp 192.168.1.100 get vrp-software.cc
<Huawei> ftp 192.168.1.100
```

#### Set Startup Software
```
<Huawei> startup system-software flash:/vrp-software.cc
<Huawei> display startup
```

### Interface Configuration

#### View Interface Status
```
<Huawei> display interface brief
<Huawei> display interface GigabitEthernet 0/0/1
```

#### Configure IP Address
```
[Huawei] interface GigabitEthernet 0/0/1
[Huawei-GigabitEthernet0/0/1] ip address 192.168.1.1 255.255.255.0
[Huawei-GigabitEthernet0/0/1] undo shutdown
```

#### Interface Description
```
[Huawei-GigabitEthernet0/0/1] description To_Core_Switch
```

### Diagnostic Commands

#### Ping
```
<Huawei> ping 192.168.1.1
<Huawei> ping -c 5 -s 1500 192.168.1.1
```

#### Traceroute
```
<Huawei> tracert 192.168.1.1
```

#### Display Commands
```
<Huawei> display version
<Huawei> display ip interface brief
<Huawei> display ip routing-table
```

### Summary

Huawei VRP provides a powerful and unified platform for network device management. Key concepts include:
- Understanding VRP architecture and file system
- Navigating between different CLI views
- Configuring basic system parameters
- Managing users and authentication
- Working with configuration files
- Performing system maintenance and upgrades
- Using diagnostic commands for troubleshooting
