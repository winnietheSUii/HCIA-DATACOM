# OSPF Process

An **OSPF process** is an instance of the [[Open Shortest Path First (OSPF)]] routing protocol running on a router.

## Key Characteristics

- **Process ID**: Unique identifier for each OSPF instance on the router
  - Locally significant only (does not need to match between routers)
  - Range: 1-65535 (Huawei VRP)
  
- **Multiple processes**: A single router can run multiple OSPF processes simultaneously
  
- **Independent operation**: Each process maintains separate:
  - [[Link-State Database (LSDB)]]
  - Routing calculations (SPF algorithm)
  - Area configuration
  - Neighbor relationships

## Use Cases

1. **Routing domain separation**: Isolate different routing domains on the same router
2. **Migration scenarios**: Run old and new OSPF configurations simultaneously
3. **Multi-tenancy**: Separate routing for different customers or departments
4. **Redistribution control**: Selective route redistribution between processes

## Configuration

**Huawei VRP**:
```
[Router] ospf 1                    # Create OSPF process 1
[Router-ospf-1] area 0
[Router-ospf-1-area-0.0.0.0] network 192.168.1.0 0.0.0.255
```

**Cisco IOS**:
```
Router(config)# router ospf 1
Router(config-router)# network 192.168.1.0 0.0.0.255 area 0
```

## Important Notes

- Process ID is **locally significant** - routers with different process IDs can still form neighbor relationships
- Neighbor relationships depend on:
  - Area ID match
  - Subnet match
  - Hello/Dead interval match
  - Authentication match
  - **NOT** Process ID match

## Related Concepts

- [[Open Shortest Path First (OSPF)]]
- [[Link-State Database (LSDB)]]
- [[Area Border Router (ABR)]]
- [[Autonomous System (AS)]]
