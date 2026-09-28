# 04 — Phase 2: Security Onion + Wazuh

**Goal:** Security Onion is capturing and analyzing traffic mirrored from OPNsense; Wazuh manager is receiving agent telemetry from at least one host; both are queryable.

## Security Onion

1. Install Security Onion on `soc-securityonion` — choose **Standalone** install mode for a homelab (combines sensor + manager roles).
2. During setup, select the monitoring interface (the NIC configured per the promiscuous-mode or mirrored-traffic approach in `02-network-design.md`) separately from the management interface.
3. Enable Suricata (signature-based NIDS) and Zeek (protocol/metadata logging) — both are default in Security Onion's standard install.
4. Confirm via the Security Onion web console (Kibana-based) that you're seeing traffic logs once other VMs are powered on and generating traffic.
5. Update Suricata rulesets (Emerging Threats Open ruleset is default and sufficient for lab use).

## Wazuh Manager

You can either:
- **(a)** Deploy Wazuh manager as its own Ubuntu Server VM (`soc-wazuh-mgr`) — cleaner separation, recommended for portfolio clarity, or
- **(b)** Use Security Onion's built-in Wazuh integration if your Security Onion version bundles it.

This doc assumes (a).

```bash
# On soc-wazuh-mgr (Ubuntu Server)
curl -sO https://packages.wazuh.com/4.x/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```

This installs Wazuh manager, indexer, and dashboard in a single-node config (fine for lab scale). Note the generated admin credentials it prints — store them in a password manager, not in the repo.

Access the dashboard at `https://10.10.30.20` (or your assigned IP).

## Agent deployment

Deploy the Wazuh agent to at least:
- `corp-win10` (Windows agent + Sysmon, see `06-phase4-windows-domain.md`)
- Optionally `dmz-web01` (Linux agent) if you want host-level telemetry from the DMZ target in addition to network-level detection

```bash
# Linux agent example
curl -o wazuh-agent.deb https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.x_amd64.deb
WAZUH_MANAGER='10.10.30.20' dpkg -i ./wazuh-agent.deb
systemctl enable wazuh-agent && systemctl start wazuh-agent
```

## Webhook prep for SOAR (Phase 3 dependency)

Wazuh supports outbound integrations. You'll configure `ossec.conf` on the manager with an `<integration>` block pointing at your n8n webhook URL — hold off on completing this until n8n is deployed in Phase 3, but note the manager's config file location now: `/var/ossec/etc/ossec.conf`.

## Verification

- [ ] Security Onion dashboard shows live Suricata/Zeek events from cross-segment test traffic
- [ ] Wazuh dashboard shows the manager's own self-monitoring events
- [ ] At least one agent shows "Active" in Wazuh's Agents view
- [ ] A test alert (e.g., a failed SSH login against a monitored host) appears in Wazuh within a few seconds

Next: `05-phase3-soar-pipeline.md`
