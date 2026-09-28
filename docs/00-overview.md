# 00 — Overview

## Purpose

This lab simulates a small enterprise network end-to-end: perimeter defense, internal segmentation, centralized logging/detection, automated response triage, and a red-team capability to validate all of it. It is built to be demoed in a portfolio, a technical interview, or a LinkedIn write-up series.

## Skills this project demonstrates

- **Network engineering:** subnetting, VLAN-equivalent segmentation, routing, NAT, firewall rule design
- **Security architecture:** DMZ design, least-privilege inter-segment rules, defense in depth
- **SOC operations:** SIEM deployment and tuning, log source onboarding, alert triage workflow design
- **Detection engineering:** mapping attack techniques to Suricata/Zeek/Wazuh detections, MITRE ATT&CK alignment
- **Security automation / SOAR:** webhook orchestration (n8n), LLM-assisted alert classification and analyst communication
- **Offensive security fundamentals:** using Kali to generate realistic attack traffic for validation (not exploitation for its own sake — every attack here exists to prove a detection works)
- **Documentation & process discipline:** the kind of runbook/evidence trail a real SOC expects

## Non-goals

- This is not a CTF and not meant to demonstrate novel exploitation techniques
- This is not a production-hardening guide — some choices (flat evaluation licenses, self-signed certs, etc.) are lab-appropriate, not enterprise-production-appropriate, and that's called out where relevant
- No real external exposure — "DMZ" here is internal-only, isolated from the internet unless you deliberately choose to port-forward for a specific demo, which is not recommended

## High-level goal statement (put this at the top of your portfolio writeup)

> Designed and built a segmented enterprise network with centralized firewall routing (OPNsense), a full detection stack (Security Onion + Wazuh), and an AI-driven SOAR pipeline that automatically classifies, explains, and routes security alerts to analysts by severity — validated end-to-end against live attack scenarios including SSH brute-force, web exploitation, and lateral movement.

## Prerequisites checklist

- [ ] Host virtualization confirmed enabled (VT-x/AMD-V)
- [ ] VirtualBox installed and extension pack installed
- [ ] ISOs downloaded: OPNsense, Security Onion, Ubuntu Server 22.04/24.04, Kali Linux, Windows Server 2022 Evaluation, Windows 11 Evaluation
- [ ] Google account + Gemini API key ready (for SOAR pipeline)
- [ ] Gmail account (or app-specific dedicated account) ready for automated alert emails
- [ ] Git repo initialized locally, `.gitignore` covers credentials/API keys/evidence with sensitive data

Next: `01-architecture.md`
