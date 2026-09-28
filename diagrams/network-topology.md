# Network Topology Diagram

```mermaid
graph TB
    WAN["WAN (VirtualBox NAT)<br/>Simulated Internet"]

    subgraph FW["OPNsense — fw-opnsense"]
        direction TB
        OPN["Multi-NIC Router/Firewall<br/>Default-deny, explicit allow"]
    end

    subgraph DMZ["DMZ — 10.10.10.0/24"]
        WEB["dmz-web01<br/>DVWA / Juice Shop"]
        META["dmz-meta<br/>Metasploitable"]
    end

    subgraph CORP["LAN-CORP — 10.10.20.0/24"]
        DC["corp-dc01<br/>Windows Server AD DS"]
        WIN["corp-win10<br/>Wazuh agent + Sysmon"]
    end

    subgraph SOC["LAN-SOC — 10.10.30.0/24"]
        SO["soc-securityonion<br/>Suricata + Zeek"]
        WZ["soc-wazuh-mgr<br/>Wazuh Manager"]
        N8N["soc-n8n<br/>n8n + Gemini SOAR"]
    end

    subgraph ATTACK["LAN-ATTACK — 10.10.40.0/24"]
        KALI["atk-kali<br/>Red team box"]
    end

    WAN --> OPN
    OPN --> DMZ
    OPN --> CORP
    OPN --> SOC
    OPN --> ATTACK

    KALI -.scoped test traffic.-> WEB
    KALI -.scoped test traffic.-> META
    OPN -."port mirror / span".-> SO

    WEB -.agent telemetry.-> WZ
    WIN -.agent telemetry.-> WZ
    WZ --> N8N
    N8N -."severity-based email".-> Analyst["Analyst Inbox (Gmail)"]

    classDef untrusted fill:#f8d7da,stroke:#842029
    classDef trusted fill:#d1e7dd,stroke:#0f5132
    classDef mgmt fill:#cfe2ff,stroke:#084298
    class DMZ,ATTACK,WAN untrusted
    class SOC,CORP trusted
```

Render this on GitHub automatically by viewing this file in the repo, or paste the code block into the [Mermaid Live Editor](https://mermaid.live) for a PNG export to attach to a LinkedIn post or resume.
