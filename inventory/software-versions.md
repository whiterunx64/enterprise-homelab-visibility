# Software & Version Register

| Component | Host | Version | Installed | Last updated | Update method | Notes |
|---|---|---|---|---|---|---|
| VirtualBox | host | | | | package manager | + Extension Pack matching version |
| OPNsense | fw-opnsense | | | | UI updater | Snapshot before upgrade |
| Security Onion | soc-securityonion | | | | soup | Snapshot before upgrade |
| Wazuh manager | soc-wazuh-mgr | | | | apt | Keep agents ≤ manager version |
| Wazuh agents | corp-win10, corp-dc01, dmz-web01 | | | | msi/apt | |
| Sysmon | corp-* | | | | manual | Config: SwiftOnSecurity |
| n8n | soc-n8n | | | | docker pull | Pin tag, don't use `latest` in final build |
| Docker | soc-n8n, dmz-web01 | | | | apt | |
| DVWA / Juice Shop | dmz-web01 | | | | docker | Vulnerable by design |
| Kali | atk-kali | | | | apt full-upgrade | |
| Gemini model | n8n workflow | | | | n8n node config | Record exact model name used in evidence runs |

## Patch cadence

- SOC stack + firewall: monthly, snapshot first, verify detections still fire after (run the SSH brute-force baseline).
- Vulnerable targets: **do not patch** the intentional vulnerabilities; patch the underlying OS only.
