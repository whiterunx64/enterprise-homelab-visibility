# 02 — Network Design (VirtualBox Implementation)

## Adapter plan per VM

VirtualBox networking modes used:
- **NAT** — only for OPNsense's WAN adapter (internet-out for updates, and for your SOAR pipeline's Gemini API calls if you route them via OPNsense's WAN)
- **Internal Network** — used for every internal segment; VMs on the same internal network name can reach each other at L2, nothing outside VirtualBox can
- **Host-only Adapter** — used for the MGMT segment (OPNsense web UI access from your host)

## fw-opnsense NIC layout

| NIC | Mode | VBox network name | Purpose |
|---|---|---|---|
| NIC1 | NAT | (default NAT) | WAN |
| NIC2 | Internal Network | `intnet-dmz` | DMZ gateway |
| NIC3 | Internal Network | `intnet-corp` | LAN-CORP gateway |
| NIC4 | Internal Network | `intnet-soc` | LAN-SOC gateway |
| NIC5 | Internal Network | `intnet-attack` | LAN-ATTACK gateway |
| NIC6 | Host-only Adapter | `vboxnet0` | MGMT access from host |

All other VMs get exactly one NIC, set to the Internal Network matching their segment (e.g., `dmz-web01` → NIC1 = Internal Network `intnet-dmz`).

## Steps (VirtualBox Manager or VBoxManage CLI)

```bash
# Create host-only network for management access
VBoxManage hostonlyif create
VBoxManage hostonlyif ipconfig vboxnet0 --ip 10.10.99.1 --netmask 255.255.255.0

# Internal networks are created implicitly the first time a VM NIC references them —
# just set the network name consistently across VMs, e.g.:
VBoxManage modifyvm "fw-opnsense" --nic2 intnet --intnet2 intnet-dmz
VBoxManage modifyvm "fw-opnsense" --nic3 intnet --intnet3 intnet-corp
VBoxManage modifyvm "fw-opnsense" --nic4 intnet --intnet4 intnet-soc
VBoxManage modifyvm "fw-opnsense" --nic5 intnet --intnet5 intnet-attack
VBoxManage modifyvm "fw-opnsense" --nic6 hostonly --hostonlyadapter6 vboxnet0

VBoxManage modifyvm "dmz-web01" --nic1 intnet --intnet1 intnet-dmz
VBoxManage modifyvm "corp-dc01" --nic1 intnet --intnet1 intnet-corp
VBoxManage modifyvm "soc-securityonion" --nic1 intnet --intnet1 intnet-soc
VBoxManage modifyvm "atk-kali" --nic1 intnet --intnet1 intnet-attack
```

Repeat the pattern for the remaining VMs per the roster in `01-architecture.md`.

## Port mirroring for Security Onion (span traffic)

VirtualBox doesn't natively support port mirroring on Internal Networks the way a physical switch would. Two practical options:

1. **Promiscuous mode trick:** Set the OPNsense-facing internal network adapters to "Allow All" promiscuous mode (`VBoxManage modifyvm <vm> --nicpromisc<N> allow-all`) and give `soc-securityonion` a second NIC on the same internal network segments you want visibility into. Since it's a shared internal network (effectively a virtual hub/switch), promiscuous mode lets it see all traffic on that segment.
2. **OPNsense plugin route:** Run Suricata directly as an OPNsense plugin on the relevant interfaces instead of a separate sensor VM — simpler, less "realistic" for portfolio purposes but far less networking pain.

Document in your `03-phase1-opnsense.md` write-up which you chose and why — this is a good interview talking point either way (tradeoffs between simplicity and realism).

## IP addressing reference table

Fill this in as you assign static IPs — keep it in the repo so it doubles as inventory documentation (a real SOC artifact).

| Hostname | Segment | IP | Role |
|---|---|---|---|
| fw-opnsense | multi | 10.10.10.1 / 10.10.20.1 / 10.10.30.1 / 10.10.40.1 | Gateway for each segment |
| dmz-web01 | DMZ | 10.10.10.10 | Vulnerable web app |
| dmz-meta | DMZ | 10.10.10.11 | Metasploitable target |
| corp-dc01 | LAN-CORP | 10.10.20.10 | AD DS |
| corp-win10 | LAN-CORP | 10.10.20.20 | Workstation |
| soc-securityonion | LAN-SOC | 10.10.30.10 | NIDS/sensor |
| soc-wazuh-mgr | LAN-SOC | 10.10.30.20 | SIEM manager |
| soc-n8n | LAN-SOC | 10.10.30.30 | SOAR orchestration |
| atk-kali | LAN-ATTACK | 10.10.40.10 | Red team box |

Next: `03-phase1-opnsense.md`
