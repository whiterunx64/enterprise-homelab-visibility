# 06 — Phase 4: Windows Domain

**Goal:** A minimal but real Active Directory domain exists in LAN-CORP, with a workstation joined and reporting to Wazuh with Sysmon-enhanced telemetry.

## corp-dc01 (Windows Server 2022)

1. Install Windows Server 2022 Evaluation, static IP `10.10.20.10`, gateway `10.10.20.1`.
2. Add roles: **Active Directory Domain Services**, **DNS Server**.
3. Promote to a new forest, e.g. domain `lab.local`.
4. Create a handful of test OUs and users (`analyst1`, `svc-wazuh`, etc.) — a domain with a couple of realistic accounts is more useful for later lateral-movement scenarios than a single default admin.

## corp-win10 (workstation)

1. Install Windows 10/11 Evaluation, static IP `10.10.20.20`, gateway `10.10.20.1`, DNS pointed at `10.10.20.10`.
2. Join to `lab.local` domain.
3. Install **Sysmon** (Microsoft Sysinternals) with a solid community config — [SwiftOnSecurity's sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config) is the standard lab choice — for high-fidelity process/network/registry event logging.
4. Install the **Wazuh agent for Windows**, pointing at `10.10.30.20` (Wazuh manager).
5. Confirm Sysmon events flow into Windows Event Log channel `Microsoft-Windows-Sysmon/Operational`, and confirm Wazuh agent config includes that channel under `<localfile>` in `ossec.conf` on the agent.

## Why Sysmon matters here

Wazuh alone gives decent host telemetry, but Sysmon is what gives you process-creation, command-line, and network-connection visibility rich enough to detect things like a suspicious PowerShell download-and-execute chain — which is exactly the kind of event your lateral-movement/AD attack scenario (`09-attack-scenarios.md`) should trigger.

## Verification

- [ ] `corp-win10` shows as domain-joined (`echo %USERDOMAIN%` returns `LAB`)
- [ ] Wazuh dashboard shows `corp-win10` as an active agent
- [ ] A test action (e.g., running `whoami /priv` or a benign PowerShell command) shows up as a Sysmon event in Wazuh within seconds
- [ ] DNS resolution for `lab.local` works correctly from the workstation

Next: `07-phase5-dmz-targets.md`
