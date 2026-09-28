# Services & Ports Register

| Host | Service | Port/Proto | Intended reachable from | Monitored by | Notes |
|---|---|---|---|---|---|
| fw-opnsense | Web UI | 443/TCP | MGMT only | OPNsense logs | Never expose on WAN/other segments |
| soc-securityonion | Web console | 443/TCP | MGMT / SOC | self | |
| soc-wazuh-mgr | Agent enrollment | 1515/TCP | CORP, DMZ agents | Wazuh | |
| soc-wazuh-mgr | Agent events | 1514/TCP | CORP, DMZ agents | Wazuh | |
| soc-wazuh-mgr | Dashboard | 443/TCP | MGMT / SOC | Wazuh | |
| soc-wazuh-mgr | Indexer API | 9200/TCP | localhost / SOC only | Wazuh | |
| soc-n8n | Editor + webhook | 5678/TCP | SOC (Wazuh manager) | Wazuh agent | Restrict webhook to Wazuh manager IP |
| corp-dc01 | DNS | 53/TCP+UDP | CORP | Sysmon/Wazuh | |
| corp-dc01 | Kerberos / LDAP / SMB | 88, 389, 445 | CORP | Sysmon/Wazuh | Lateral-movement target |
| dmz-web01 | HTTP (DVWA) | 80/TCP | ATTACK, (optional) CORP | Suricata, Wazuh (opt.) | Intentionally vulnerable |
| dmz-web01 | HTTP (Juice Shop) | 3000/TCP | ATTACK | Suricata | Intentionally vulnerable |
| dmz-web01 | SSH | 22/TCP | ATTACK | Wazuh agent | Brute-force scenario target |
| dmz-meta | Multiple legacy services | various | ATTACK | Suricata | Intentionally vulnerable |

## Review process

Monthly: run `nmap -sV` from a management-side host against each segment and diff against this table. Any unexpected open port = update table **or** fix the host.
