# LinkedIn Journal Templates

Draft one post per phase while the work is fresh, then space out publishing (roughly every few days to a week) so the series reads as sustained progress rather than a single dump. Attach 1–2 screenshots or the Mermaid diagram export per post. Fill in the [bracketed] parts.

---

## Template — Kickoff / Phase 0

🏗️ Starting a build: an enterprise-style homelab for network security & SOC operations

Over the next few weeks I'm building out a segmented lab network to practice the kind of work a security operations/network engineering role actually involves — not just spinning up isolated VMs, but a real perimeter, real segmentation, centralized detection, and automated triage.

Stack:
🔹 OPNsense — perimeter firewall/router, segmenting DMZ / Corporate LAN / SOC LAN / Attacker LAN
🔹 Security Onion (Suricata + Zeek) — network detection
🔹 Wazuh — SIEM / host telemetry
🔹 n8n + Gemini — AI-driven SOAR pipeline for automated alert triage
🔹 Windows AD, vulnerable DMZ targets, Kali — for generating and validating real attack traffic

Full build documented on GitHub as I go: [repo link]

Follow along — next up: getting OPNsense routing and segmentation live.

#homelab #cybersecurity #networksecurity #SOC #blueteam

---

## Template — Phase 1 (OPNsense)

🔥 Phase 1 done: enterprise-style network segmentation with OPNsense

Instead of one flat network, this lab now has [N] distinct segments — DMZ, Corporate LAN, SOC LAN, and an isolated Attacker LAN — all routed through a central OPNsense firewall with default-deny rules.

Key design decision: every segment can only reach what it explicitly needs to. DMZ can't touch the corporate LAN. The SOC network only accepts inbound agent telemetry, it doesn't get "trusted" access outward. [Add one specific rule or tradeoff you made.]

This is the boring-but-critical part of the build — no amount of SIEM tuning matters if the network itself is flat.

[Screenshot: rule table]

Next: standing up Security Onion + Wazuh for detection.

#OPNsense #firewall #networksegmentation #cybersecurity

---

## Template — Phase 2 (SIEM)

📡 Phase 2: detection stack is live

Security Onion (Suricata + Zeek) is now watching traffic across every segment, and Wazuh is collecting host-level telemetry from the first onboarded agent.

[One sentence on something you learned/tuned — e.g., "Had to adjust the promiscuous-mode NIC setup to actually get cross-segment visibility in VirtualBox — worth documenting since it's not obvious from the docs."]

[Screenshot: dashboard with live alert]

Next: this is where it gets interesting — wiring Wazuh alerts into an AI-driven triage pipeline instead of just forwarding raw alerts to an inbox.

#SIEM #SecurityOnion #Wazuh #threatdetection

---

## Template — Phase 3 (SOAR) — your flagship post

🤖 Built an AI-powered SOAR pipeline: Wazuh + n8n + Gemini

Instead of just forwarding raw security alerts, this pipeline actively analyzes them — classifying severity (High/Medium/Low), generating plain-English explanations, and recommending response actions — then auto-sends a formatted alert email straight to the analyst's inbox based on threat level.

✅ Real-time SIEM detection with Wazuh
✅ Webhook-based orchestration via n8n
✅ AI-driven triage using Gemini
✅ Automated severity-based email alerts
✅ Validated against a live SSH brute-force attack, end to end

Moving from passive alerting → intelligent, automated SOC triage.

[Screenshot: n8n workflow canvas]
[Screenshot: resulting email]

Full technical writeup + workflow structure in the repo: [link]

#SOAR #automation #AI #cybersecurity #n8n

---

## Template — Phase 4 (AD)

🪟 Phase 4: Windows AD domain online, endpoint visibility added

Added a small Active Directory domain and a workstation with Sysmon + the Wazuh agent — this gives the lab real endpoint telemetry (process creation, command-line logging) on top of the network-level detection from Phase 2.

[One sentence: e.g., "This is the layer that will matter most for the lateral-movement scenario — network segmentation stops a lot, but you still want to see it on the host if something gets through."]

#ActiveDirectory #Sysmon #endpointdetection

---

## Template — Phase 5 (DMZ targets)

🎯 Phase 5: intentionally vulnerable DMZ targets deployed

Added [DVWA/Juice Shop/Metasploitable] to the DMZ segment — isolated, monitored, and about to get attacked on purpose.

Confirmed containment first: these hosts can be reached from the attacker segment, but cannot reach the corporate LAN or SOC network, even in a worst-case compromise scenario. [mention the specific test you ran]

Next: running real attack scenarios and validating the whole detection chain.

#appsec #DVWA #pentesting

---

## Template — Phase 6 (Attack validation) — second flagship post

⚔️ Validating the whole stack: attacking my own lab (on purpose)

Ran [N] MITRE ATT&CK-mapped scenarios against the lab from Kali — SSH brute-force, network scanning, web app exploitation, and a lateral-movement containment test — and traced each one through the full detection → triage → alert chain.

Highlight: the lateral-movement test. With the firewall's default-deny rules in place, a "compromised" DMZ host could not reach the corporate network at all. Only when I deliberately opened a scoped rule to simulate a misconfiguration did the attack succeed — and even then, Wazuh/Sysmon caught it and the SOAR pipeline flagged it High severity within seconds.

That contrast — attack fails under correct config, attack succeeds and gets caught under misconfig — is the whole point of defense in depth, made concrete.

Full scenario list + evidence in the repo: [link]

#redteam #blueteam #MITREATT&CK #detectionengineering

---

## Template — Wrap-up / project complete

✅ Enterprise homelab build complete — full writeup + repo live

Over [N weeks], I designed and built a segmented network (OPNsense), a full detection stack (Security Onion + Wazuh), an AI-driven SOAR pipeline (n8n + Gemini) for automated alert triage, and validated the whole thing against real attack scenarios mapped to MITRE ATT&CK.

What this project demonstrates:
- Network segmentation & firewall rule design
- SIEM/NIDS deployment and tuning
- Security automation (SOAR) with LLM-assisted triage
- Detection engineering validated against real attack traffic, not assumed

Everything — architecture docs, firewall rules, workflow design, attack scenarios, and evidence — is documented in the repo: [link]

Open to feedback from anyone who's built something similar, or roles where this kind of work is the day job.

#cybersecurity #SOC #portfolio #opentowork
