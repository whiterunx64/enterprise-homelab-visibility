# Host Register

| ID | Hostname | Role | OS / Version | Segment | IP | vCPU | RAM | Disk | Wazuh Agent | Sysmon | Status | Snapshot |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| H01 | fw-opnsense | Perimeter firewall/router | OPNsense [ver] | multi | 10.10.x.1 (per segment) | 2 | 2GB | 20GB | n/a | n/a | planned | |
| H02 | soc-securityonion | NIDS/sensor (Suricata+Zeek) | Security Onion [ver] | LAN-SOC | 10.10.30.10 | 4 | 8-12GB | 100GB | n/a | n/a | planned | |
| H03 | soc-wazuh-mgr | SIEM manager/indexer/dashboard | Ubuntu Server [ver] + Wazuh [ver] | LAN-SOC | 10.10.30.20 | 2 | 4GB | 40GB | n/a (manager) | n/a | planned | |
| H04 | soc-n8n | SOAR orchestration | Ubuntu Server [ver] + Docker + n8n | LAN-SOC | 10.10.30.30 | 2 | 2GB | 20GB | yes | n/a | planned | |
| H05 | corp-dc01 | Active Directory DC / DNS | Windows Server 2022 Eval | LAN-CORP | 10.10.20.10 | 2 | 4GB | 60GB | yes | yes | planned | |
| H06 | corp-win10 | Domain workstation | Windows 10/11 Eval | LAN-CORP | 10.10.20.20 | 2 | 4GB | 60GB | yes | yes | planned | |
| H07 | dmz-web01 | Vulnerable web app (DVWA/Juice Shop) | Ubuntu Server [ver] + Docker | DMZ | 10.10.10.10 | 1 | 2GB | 20GB | optional | n/a | planned | |
| H08 | dmz-meta | Metasploitable target | Metasploitable [2/3] | DMZ | 10.10.10.11 | 1 | 1GB | 20GB | no (unsupported) | n/a | planned | |
| H09 | atk-kali | Red-team workstation | Kali Linux [ver] | LAN-ATTACK | 10.10.40.10 | 2 | 4GB | 40GB | no | n/a | planned | |

## Per-host detail template

Copy this block for each host as you build it.

```
### <hostname>
- Purpose:
- Built on (date):
- ISO/source + checksum verified: [ ] yes
- Hardening applied: (e.g., SSH keys only, unattended-upgrades, firewall)
- Intentionally vulnerable?: yes/no  (if yes, why, and what isolates it)
- Log sources shipped: (agent / syslog / none)
- Baseline snapshot name:
- Owner: (you)
- Notes:
```

## Resource budget

| Metric | Total allocated | Host capacity | Headroom |
|---|---|---|---|
| vCPU | 18 | [fill in] | |
| RAM | ~31GB | [fill in] | |
| Disk | ~380GB | [fill in] | |

If headroom is negative, define **run profiles** in `change-log.md` (e.g., *Core*: fw + SOC stack; *Exercise*: Core + DMZ + Kali; *Full*: everything).
