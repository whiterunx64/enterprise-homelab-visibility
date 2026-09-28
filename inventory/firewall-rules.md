# Firewall Rule Register (OPNsense)

Mirror of the rules described in `docs/03-phase1-opnsense.md`. Every rule gets an ID, a reason, and a test.

| ID | Interface | Action | Source | Destination | Port/Proto | Log | Purpose | Test evidence | Status |
|---|---|---|---|---|---|---|---|---|---|
| FW-001 | DMZ | Allow | DMZ net | WAN | 80,443/TCP | Y | Package updates | | planned |
| FW-002 | DMZ | Allow | DMZ net | 10.10.30.20 | 1514,1515/TCP | Y | Wazuh agent telemetry | | planned |
| FW-003 | DMZ | **Deny** | DMZ net | CORP net | any | Y | Contain DMZ compromise | | planned |
| FW-004 | DMZ | **Deny** | DMZ net | SOC net | any | Y | Protect SOC | | planned |
| FW-010 | CORP | Allow | CORP net | WAN | 80,443/TCP | Y | Updates | | planned |
| FW-011 | CORP | Allow | CORP net | 10.10.30.20 | 1514,1515/TCP | Y | Wazuh agents | | planned |
| FW-012 | CORP | **Deny** | CORP net | DMZ / ATTACK | any | Y | Segmentation | | planned |
| FW-020 | SOC | Allow | 10.10.30.30 | WAN | 443/TCP | Y | n8n → Gemini/Gmail | | planned |
| FW-021 | SOC | Allow | SOC net | WAN | 80,443/TCP | Y | Updates | | planned |
| FW-022 | SOC | **Deny** | SOC net | CORP/DMZ/ATTACK | any | Y | SOC does not initiate laterally | | planned |
| FW-030 | ATTACK | Allow | 10.10.40.10 | DMZ net | any | Y | Test corridor | | planned |
| FW-031 | ATTACK | **Deny** | ATTACK net | CORP/SOC | any | Y | Default containment | | planned |
| FW-099 | any | **Deny** | any | any | any | Y | Explicit default deny | | planned |

## Temporary rules (exercise-only)

| ID | Opened | Closed | Rule | Scenario | Evidence |
|---|---|---|---|---|---|
| FW-T01 | | | DMZ → CORP SMB (445) | Lateral movement test | |

Every temporary rule **must** have a closed date. An open temp rule with no close date is a finding.
