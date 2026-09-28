# Enterprise Homelab — Network Security & SOC Operations Lab

A self-hosted, segmented enterprise network built for practicing network security engineering, SOC operations, and detection engineering. This repo documents the full build: architecture, phased deployment, attack scenarios, and an AI-driven SOAR pipeline (Wazuh + n8n + Gemini) for automated alert triage.

**Portfolio goal:** demonstrate hands-on ability to design segmented enterprise networks, deploy and tune a SIEM/NIDS stack, build detection-to-response automation, and validate it against real attack techniques (MITRE ATT&CK-mapped).

---

## Why this project

Most homelabs are flat networks with a couple of VMs. This one is built to *look and behave like a small enterprise*:

- A real perimeter firewall/router (OPNsense) segmenting WAN / DMZ / Corporate LAN / SOC LAN / Attacker LAN
- Centralized detection (Security Onion: Suricata + Zeek) and a SIEM (Wazuh)
- An automated triage layer (SOAR) that classifies severity, explains alerts in plain English, and emails analysts — instead of just forwarding raw alerts
- Intentionally exposed, vulnerable services in a DMZ
- A red-team box (Kali) used to generate real traffic that the blue-team stack has to detect

## Repo structure

```
homelab-enterprise-soc/
├── README.md                      ← you are here
├── docs/
│   ├── 00-overview.md             ← goals, skills demonstrated, prerequisites
│   ├── 01-architecture.md         ← network diagram description, VM roster, IP plan
│   ├── 02-network-design.md       ← VirtualBox networking, VLANs, OPNsense interfaces
│   ├── 03-phase1-opnsense.md      ← firewall/router build
│   ├── 04-phase2-siem.md          ← Security Onion + Wazuh manager
│   ├── 05-phase3-soar-pipeline.md ← n8n + Gemini automated triage
│   ├── 06-phase4-windows-domain.md← AD DS + Wazuh Windows agents
│   ├── 07-phase5-dmz-targets.md   ← exposed vulnerable services
│   ├── 08-phase6-kali-attacks.md  ← attacker VM setup
│   ├── 09-attack-scenarios.md     ← scripted attacks mapped to detections
│   └── 10-validation-checklist.md ← proof-of-detection checklist, screenshots log
├── journal/
│   └── linkedin-templates.md      ← post drafts per phase, for public build-log
└── diagrams/
    └── network-topology.md        ← Mermaid diagram source (renders on GitHub)
```

## Build phases (also your portfolio narrative arc)

| Phase | Deliverable | Status |
|---|---|---|
| 0 | Architecture & IP plan finalized | ☐ |
| 1 | OPNsense routing/segmentation live | ☐ |
| 2 | Security Onion + Wazuh manager collecting logs | ☐ |
| 3 | SOAR pipeline (n8n + Gemini) triaging & emailing alerts | ☐ |
| 4 | Windows AD domain + agents reporting | ☐ |
| 5 | DMZ vulnerable targets exposed & monitored | ☐ |
| 6 | Kali attack scenarios executed & validated end-to-end | ☐ |
| 7 | Documentation + LinkedIn journal published | ☐ |

Check these off in this README as you go — GitHub renders checkboxes, and it doubles as a visible progress tracker for anyone viewing the repo (recruiters included).

## How to use this repo

1. Read `docs/00-overview.md` and `docs/01-architecture.md` first — don't build VMs before the network plan is fixed on paper.
2. Follow `docs/03` → `docs/08` in order. Each phase assumes the previous one is working and validated — don't skip ahead.
3. Use `docs/10-validation-checklist.md` as your test plan for each phase; screenshot evidence goes in `diagrams/` or a `evidence/` folder you add locally (gitignored if it contains sensitive output).
4. Use `journal/linkedin-templates.md` to draft a post at the end of each phase while the details are fresh.

## Prerequisites

- Host machine: 32GB+ RAM recommended (16GB minimum if running VMs sequentially rather than concurrently), 200GB+ free disk, virtualization enabled in BIOS
- VirtualBox 7.x
- ISOs: OPNsense, Security Onion, Ubuntu Server, Kali Linux, Windows Server evaluation, Windows 10/11 evaluation
- Basic familiarity with Linux CLI, networking fundamentals (subnetting, routing, firewall concepts)

## License / disclaimer

This lab is for personal education and portfolio demonstration only, fully isolated from any production network. Attack techniques are executed only against intentionally vulnerable systems inside this lab. Do not point any of these tools at systems you don't own or have explicit authorization to test.
