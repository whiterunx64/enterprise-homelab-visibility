# 01 — Architecture

## Network segments

| Segment | Purpose | Example CIDR | Trust level |
|---|---|---|---|
| WAN | Simulated internet uplink (VirtualBox NAT) | DHCP from VBox NAT | Untrusted |
| DMZ | Exposed/vulnerable services | 10.10.10.0/24 | Untrusted-facing, monitored |
| LAN-CORP | Windows AD, workstations | 10.10.20.0/24 | Semi-trusted |
| LAN-SOC | Security Onion, Wazuh manager, n8n | 10.10.30.0/24 | Trusted, restricted access |
| LAN-ATTACK | Kali | 10.10.40.0/24 | Untrusted, isolated except for scoped paths to DMZ |
| MGMT | OPNsense web UI, VBox host-only mgmt | 10.10.99.0/24 | Trusted, host-only |

Adjust the third octet scheme to taste — the point is every segment is a distinct broadcast domain behind OPNsense, not a flat 192.168.1.0/24 for everything.

## VM roster

| VM | OS | Segment(s) | vCPU | RAM | Disk |
|---|---|---|---|---|---|
| fw-opnsense | OPNsense | WAN + all internal segments (multi-NIC) | 2 | 2GB | 20GB |
| soc-securityonion | Security Onion | LAN-SOC | 4 | 8GB (12GB+ ideal) | 100GB |
| soc-wazuh-mgr | Ubuntu Server (or role on Security Onion) | LAN-SOC | 2 | 4GB | 40GB |
| soc-n8n | Ubuntu Server + Docker (n8n) | LAN-SOC | 2 | 2GB | 20GB |
| corp-dc01 | Windows Server 2022 (AD DS) | LAN-CORP | 2 | 4GB | 60GB |
| corp-win10 | Windows 10/11 + Wazuh agent + Sysmon | LAN-CORP | 2 | 4GB | 60GB |
| dmz-web01 | Ubuntu + DVWA/Juice Shop | DMZ | 1 | 2GB | 20GB |
| dmz-meta | Metasploitable2/3 | DMZ | 1 | 1GB | 20GB |
| atk-kali | Kali Linux | LAN-ATTACK | 2 | 4GB | 40GB |

Total if run concurrently: ~19 vCPU / ~31GB RAM. If your host can't handle that, run in clusters by phase (SOC stack always on, spin up DMZ+Kali only during exercises).

## Traffic flow principle

**Everything must route through OPNsense.** No two VMs in different segments should be able to reach each other directly — VirtualBox internal networks per segment + OPNsense as the only multi-homed router enforces this. This is what makes Suricata/Zeek on the SOC segment (via port mirror/span or inline on OPNsense) actually see cross-segment traffic.

## Monitoring placement

Two valid options — pick one and document which in your writeup:

1. **Inline/span on OPNsense:** Configure a mirror port or use OPNsense's built-in Suricata/Zeek plugins directly on the firewall. Simplest for a homelab.
2. **Dedicated sensor:** Security Onion as a standalone sensor+manager, with an OPNsense-side port mirror feeding it. More "real" SOC topology, more setup work.

This doc set assumes option 2 (dedicated sensor) since it's the stronger portfolio story.

## Firewall rule philosophy (document actual rules in `03-phase1-opnsense.md`)

- Default deny, explicit allow
- DMZ → LAN-CORP: deny everything (this is the rule your lateral-movement scenario tries to break)
- DMZ → LAN-SOC: deny everything except agent-to-manager telemetry ports if you place a Wazuh agent in DMZ
- LAN-ATTACK → DMZ: allow (this is your test path)
- LAN-ATTACK → LAN-CORP/LAN-SOC: deny by default; only opened deliberately for specific lateral-movement exercises, then closed again
- All segments → LAN-SOC: deny except required log-shipping/agent ports
- MGMT: host-only, not reachable from any VM segment

## Diagram

See `diagrams/network-topology.md` for a Mermaid source diagram (renders natively on GitHub).

Next: `02-network-design.md`
