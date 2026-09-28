# 03 — Phase 1: OPNsense Router/Firewall

**Goal:** OPNsense routes and segments all traffic; default-deny rules enforced; each segment can reach WAN but not each other except where explicitly allowed.

## Steps

1. Install OPNsense on `fw-opnsense` with the NIC layout from `02-network-design.md`.
2. During install, assign interfaces: WAN = NIC1, and assign OPT1–OPT4 to NIC2–NIC5 (DMZ, CORP, SOC, ATTACK). Rename them in the UI (Interfaces → Assignments) to `DMZ`, `CORP`, `SOC`, `ATTACK` for clarity.
3. Set static IPs on each internal interface matching the gateway addresses in your IP table.
4. Enable DHCP server per internal interface (Services → DHCPv4) scoped to each segment's /24, or use static IPs on lab VMs (recommended for a documented inventory — static is easier to reference in Suricata alerts and Wazuh agent configs).
5. Configure NAT: outbound NAT on WAN so internal segments can reach the internet for updates. Confirm under Firewall → NAT → Outbound.

## Firewall rules to create (Firewall → Rules → per interface)

Work through rules interface-by-interface. OPNsense evaluates rules top-down per interface, default deny at the bottom.

**DMZ interface:**
- Allow DMZ → WAN (updates) — TCP 80/443 only, not "any"
- Allow DMZ → SOC:  only Wazuh agent port (1514/1515 TCP) if you run an agent on DMZ hosts
- Deny DMZ → CORP (explicit deny, logged)
- Deny DMZ → SOC (any other port)

**CORP interface:**
- Allow CORP → WAN (updates)
- Allow CORP → SOC: Wazuh agent ports only
- Deny CORP → DMZ
- Deny CORP → ATTACK

**SOC interface:**
- Allow SOC → WAN (for Gemini API calls from n8n, and package updates) — scope to HTTPS/443 outbound if you want to be strict
- Deny SOC → everything else inbound-initiated (SOC should only receive agent connections, not initiate into other segments, except its own health checks)

**ATTACK interface:**
- Allow ATTACK → DMZ (this is your test corridor — leave broad here since you want Kali to freely test DMZ)
- Deny ATTACK → CORP (default; you'll open a narrow, temporary rule only when running the lateral-movement scenario in `09-attack-scenarios.md`, then close it again — document this open/close cycle, it's a great "least privilege in practice" portfolio note)
- Deny ATTACK → SOC

**Logging:** Enable logging on every deny rule (not just allow). This is what feeds Suricata/Wazuh with firewall-level events, and it's what you'll screenshot for `10-validation-checklist.md`.

## Verification

- [ ] From `atk-kali`, `ping` and `curl` a DMZ host — succeeds
- [ ] From `atk-kali`, attempt to reach `corp-dc01` — fails (logged deny)
- [ ] From `dmz-web01`, attempt to reach `corp-dc01` — fails (logged deny)
- [ ] All segments reach WAN for `apt update` / equivalent
- [ ] OPNsense dashboard shows traffic graphs per interface

## Portfolio note

Export your firewall rule set (Diagnostics → or a `Firewall → Rules` screenshot) and include it as evidence — a documented, least-privilege rule set is one of the most concrete things you can show a hiring manager.

Next: `04-phase2-siem.md`
