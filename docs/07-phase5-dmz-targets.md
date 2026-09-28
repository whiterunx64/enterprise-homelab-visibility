# 07 — Phase 5: DMZ Exposed / Vulnerable Targets

**Goal:** Intentionally vulnerable, isolated targets exist in the DMZ for Kali to attack, with traffic visible to Security Onion and (optionally) host telemetry visible to Wazuh.

## dmz-web01 — vulnerable web app host

```bash
# Ubuntu Server base, static IP 10.10.10.10
sudo apt update && sudo apt install -y docker.io
sudo docker run -d -p 80:80 vulnerables/web-dvwa
# OR, for a more modern app-sec target:
sudo docker run -d -p 3000:3000 bkimminich/juice-shop
```

- DVWA: classic SQLi/XSS/command-injection training target, good for mapping to Suricata signatures
- Juice Shop: OWASP Top 10-aligned modern web app, good if you want to demonstrate broader web app security knowledge

Pick one or run both on different ports. Optionally install the Wazuh Linux agent here too, for host-level log correlation alongside the network-level Suricata detections.

## dmz-meta — Metasploitable

Download Metasploitable2 (or 3) as a pre-built VM image, import into VirtualBox, set NIC to `intnet-dmz`, static IP `10.10.10.11`. This gives you a target with a wide spread of deliberately vulnerable services (FTP, Samba, distcc, etc.) — useful for demonstrating breadth of scanning/exploitation detection beyond just web app attacks.

## OPNsense rule check specific to this phase

Confirm (from `03-phase1-opnsense.md`) that:
- ATTACK → DMZ is allowed (your test path)
- DMZ → CORP and DMZ → SOC remain denied — this matters because a compromised DMZ box should not be able to pivot, and proving that containment holds is a key part of your validation story

## Verification

- [ ] `dmz-web01` reachable from `atk-kali` on its service ports
- [ ] `dmz-web01` NOT reachable from `corp-win10` or `corp-dc01`
- [ ] Security Onion logs show HTTP traffic to `dmz-web01` when browsed/scanned from Kali
- [ ] (If agent installed) Wazuh shows host-level events from `dmz-web01`

Next: `08-phase6-kali-attacks.md`
