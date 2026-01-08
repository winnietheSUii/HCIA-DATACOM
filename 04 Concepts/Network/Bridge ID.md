# Bridge ID

Unique identifier for each switch in [[STP]]/[[RSTP]], used to elect [[Root Bridge]] and break ties in path selection.

## Format

**Bridge ID = Priority (2 bytes) + MAC Address (6 bytes)**

Example: `32768.00-11-22-33-44-55`

- **Priority**: 0-61440, default 32768
  - Must be multiple of 4096
  - Lower is preferred
  - Configured administratively

- **MAC Address**: Switch's base MAC
  - Lowest on switch (from pool)
  - Tiebreaker if priorities equal

## Priority Values

| Priority | Use Case |
|----------|----------|
| 0 | Primary root bridge |
| 4096 | Secondary/backup root |
| 8192-28672 | Intermediate preference |
| 32768 | Default (neutral) |
| 61440 | Never want as root |

## Extended System ID (PVST+/MSTP)

In per-VLAN/instance STP:
- Priority field split: **4-bit priority** + **12-bit VLAN/Instance ID**
- Effective priority = Base priority + VLAN ID
- Example: Base 32768 + VLAN 10 = 32778

## Configuration (Huawei VRP)

```
# Set bridge priority
[Huawei] stp priority 4096

# Per-instance priority (MSTP)
[Huawei] stp instance 1 priority 8192
```

## Verification

```
display stp brief
  - Shows local Bridge ID
  - Shows root Bridge ID
  - Role and state of ports
```

## Related Concepts

- [[STP]]
- [[Root Bridge]]
- [[BPDU]]
- [[RSTP]]
- [[MSTP]]
