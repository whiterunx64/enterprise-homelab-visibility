# 08 — Phase 6: Kali (Attacker) Setup

**Goal:** Kali is positioned in LAN-ATTACK, can reach DMZ per firewall policy, and is ready to run the scripted scenarios in `09-attack-scenarios.md`.

## Setup

1. Install Kali Linux, static IP `10.10.40.10`, gateway `10.10.40.1`.
2. Confirm default tools present: `nmap`, `hydra`, `metasploit-framework`, `sqlmap`, `nikto`, `responder`, `crackmapexec`/`netexec`, `bloodhound` (for the AD scenario).
3. `sudo apt update && sudo apt full-upgrade -y` — keep it current before recording any evidence, so screenshots reflect a maintained toolset.

## Ground rules for this lab (document these — they matter for portfolio credibility)

- Every target is owned by you and exists solely inside this lab
- No attack traffic ever traverses the WAN/NAT interface toward anything outside your lab
- Every scenario run is logged: timestamp, command used, expected detection, actual detection screenshot

## Suggested evidence log format

Keep a simple table (or expand `10-validation-checklist.md`) per attack run:

| Date | Scenario | Command | Wazuh alert? | SOAR email sent? | Notes |
|---|---|---|---|---|---|
| | SSH brute-force | `hydra -l root -P rockyou.txt ssh://10.10.10.10` | ✅ level 10 | ✅ High | |

This table is what turns "I ran some attacks" into "I validated detection engineering with reproducible evidence" — the second one is what a portfolio needs.

Next: `09-attack-scenarios.md`
