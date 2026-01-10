# Accounting

Third pillar of [[AAA]]: **Track user actions** for audit, compliance, and billing.

## Information Logged

- **Login/logout**: User, timestamp, success/failure
- **Commands executed**: Command text, result, time
- **Configuration changes**: What was changed, by whom, when
- **Session duration**: Start/end time, idle time
- **Bandwidth usage**: Per-user data consumed (optional)

## Accounting Servers

### RADIUS Accounting
- **Port**: UDP 1813 (standard), 1814 (Huawei alternate)
- **Messages**: Start (on login), Stop (on logout/session end), Interim (periodic updates)
- **Data**: Sent in RADIUS Accounting-Request packets

Configuration:
```
[Device-aaa] radius-scheme ACCT_SCHEME
[Device-aaa-radius] server-ip 10.0.0.2 server-port 1813
[Device-aaa-radius] shared-key MySecret123
[Device-aaa] accounting-scheme ACCT_SCHEME
[Device-aaa-accounting] accounting-mode radius local
[Device] line vty 0 4
[Device-line-vty0-4] accounting-mode scheme ACCT_SCHEME
```

### TACACS+ Accounting
- **Port**: TCP 49
- **Finer granularity**: Per-command accounting (not just session start/stop)
- **More detailed** than RADIUS

Configuration:
```
[Device-aaa] tacacs-scheme TACACS_SCHEME
[Device-aaa-tacacs] server-ip 10.0.0.3 server-port 49
[Device-aaa-tacacs] shared-key MySecret123
[Device-aaa] accounting-scheme ACCT_SCHEME
[Device-aaa-accounting] accounting-mode tacacs+ local
```

## Local Accounting

Fallback if external server unreachable:
```
[Device-aaa-accounting] accounting-mode radius local
; If RADIUS fails, log locally to device buffer
```

**View local logs**:
```
display accounting log
```

## Compliance & Audit

**Use cases**:
- **Regulatory compliance**: Track all changes (SOC 2, HIPAA, PCI-DSS)
- **Forensics**: Identify who made changes in case of incident
- **Billing**: Track per-user bandwidth consumption
- **Security monitoring**: Detect unauthorized access attempts

## Best Practices

- **Enable accounting on all user accounts**: Don't skip admin
- **Retain logs**: Archive accounting data for 1+ year
- **Monitor for anomalies**: Look for unusual login times, failed attempts
- **Review periodically**: Check logs for unauthorized access
- **Separate servers**: Use different RADIUS/TACACS+ servers for auth and accounting (optional)

## Related
- [[AAA]]
- [[RADIUS]]
- [[TACACS+]]
- [[Authentication]]
- [[Authorization]]
