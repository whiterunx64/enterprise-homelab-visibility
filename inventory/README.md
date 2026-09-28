# Inventory

Single source of truth for every asset in the lab. Treat it like a real CMDB: **if it's running, it's listed here; if it's changed, `change-log.md` says when and why.**

| File | Contents |
|---|---|
| `hosts.md` | Human-readable VM/asset register (specs, role, owner, status) |
| `hosts.yml` | Machine-readable version (Ansible-style; reusable for automation later) |
| `networks.md` | Segments, CIDRs, gateways, VirtualBox network names, DHCP/static policy |
| `services-ports.md` | Every listening service per host, intended exposure, monitoring coverage |
| `firewall-rules.md` | OPNsense rule register with IDs, purpose, and test evidence |
| `software-versions.md` | OS/tool versions and update cadence |
| `monitoring-coverage.md` | Which host is covered by which sensor/log source (gap analysis) |
| `credentials-policy.md` | How secrets are handled (no secrets stored in this repo) |
| `change-log.md` | Dated record of every infrastructure change |

## Rules

1. Update inventory **in the same commit** as the change it describes.
2. Never put passwords, API keys, or tokens here. See `credentials-policy.md`.
3. Status values: `planned` → `building` → `active` → `stopped` → `decommissioned`.
