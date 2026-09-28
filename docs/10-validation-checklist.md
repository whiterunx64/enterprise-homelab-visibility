# 10 — Validation Checklist & Evidence Log

Use this as your final pass before calling a phase "done" and posting about it.

## Phase-by-phase sign-off

### Phase 1 — OPNsense
- [ ] Default-deny confirmed on all inter-segment rules except documented exceptions
- [ ] Logging enabled on deny rules
- [ ] Screenshot: firewall rule table per interface

### Phase 2 — Security Onion + Wazuh
- [ ] Live traffic visible in Security Onion within seconds of generation
- [ ] At least one active Wazuh agent
- [ ] Screenshot: Security Onion dashboard with real alert; Wazuh dashboard with active agent list

### Phase 3 — SOAR
- [ ] End-to-end chain confirmed: Wazuh alert → n8n → Gemini → email
- [ ] Severity branching confirmed with at least one High and one Low test case
- [ ] Screenshot: n8n workflow canvas; received email

### Phase 4 — Windows Domain
- [ ] Domain join confirmed
- [ ] Sysmon events flowing into Wazuh
- [ ] Screenshot: Wazuh agent list showing `corp-win10` active

### Phase 5 — DMZ
- [ ] Segmentation confirmed (DMZ isolated from CORP/SOC)
- [ ] Screenshot: failed connection attempt DMZ → CORP (logged deny)

### Phase 6 — Attack Validation
- [ ] All 4–5 scenarios from `09-attack-scenarios.md` executed
- [ ] Evidence table fully populated
- [ ] Screenshots collected: command, alert, email — per scenario

## Final portfolio deliverables checklist

- [ ] This repo pushed to GitHub, README polished, diagrams rendering
- [ ] Evidence screenshots either embedded in docs or linked from an `evidence/` folder (redact any real personal info — API keys, real IPs if you ever bridge to a public network, etc.)
- [ ] LinkedIn posts drafted per `journal/linkedin-templates.md` and published across the build (not all at once — spacing them out shows sustained work)
- [ ] One-paragraph project summary ready to paste into resume/portfolio site (draft below)

### Resume/portfolio summary draft

> Designed and deployed a segmented enterprise homelab (OPNsense firewall, Security Onion NIDS, Wazuh SIEM, Windows AD domain, DMZ targets) and built an AI-powered SOAR pipeline (n8n + Gemini) that automatically classifies alert severity, generates analyst-readable explanations, and sends severity-routed email alerts. Validated the full detection-to-response chain against live attack scenarios (SSH brute-force, web exploitation, network scanning, lateral movement) mapped to MITRE ATT&CK, confirming both detection coverage and network containment.
