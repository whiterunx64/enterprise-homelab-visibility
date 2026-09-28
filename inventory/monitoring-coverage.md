# Monitoring Coverage Matrix

Shows what can see what. Gaps are documented, not hidden.

| Host | Network (Suricata/Zeek) | Host logs (Wazuh) | Sysmon | Firewall logs | SOAR-eligible alerts |
|---|---|---|---|---|---|
| fw-opnsense | n/a | syslog forward (optional) | n/a | native | yes |
| soc-securityonion | self | n/a | n/a | n/a | via Suricata → (future) |
| soc-wazuh-mgr | ✅ | self-monitoring | n/a | ✅ | yes |
| soc-n8n | ✅ | agent | n/a | ✅ | yes |
| corp-dc01 | ✅ | ✅ | ✅ | ✅ | yes |
| corp-win10 | ✅ | ✅ | ✅ | ✅ | yes |
| dmz-web01 | ✅ | optional | n/a | ✅ | yes |
| dmz-meta | ✅ | ❌ (no agent) | n/a | ✅ | network only |
| atk-kali | ✅ | ❌ | n/a | ✅ | n/a |

## Known gaps (fill in honestly — this is a strength in interviews)

- Suricata alerts are not yet routed into the n8n pipeline (only Wazuh alerts are). → *Future improvement: forward Security Onion alerts to a second webhook.*
- Metasploitable cannot run a modern agent; coverage is network-only.
- Encrypted traffic (HTTPS) limits payload inspection on Suricata.
