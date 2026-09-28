# Network Register

| Segment | VBox network | CIDR | Gateway (OPNsense) | Addressing | DHCP range | Trust | Internet access |
|---|---|---|---|---|---|---|---|
| WAN | NAT | VBox default | n/a | DHCP | n/a | Untrusted | n/a |
| DMZ | intnet-dmz | 10.10.10.0/24 | 10.10.10.1 | Static | none | Untrusted-facing | HTTP/HTTPS only |
| LAN-CORP | intnet-corp | 10.10.20.0/24 | 10.10.20.1 | Static | none | Semi-trusted | HTTP/HTTPS only |
| LAN-SOC | intnet-soc | 10.10.30.0/24 | 10.10.30.1 | Static | none | Trusted | HTTPS (Gemini/Gmail, updates) |
| LAN-ATTACK | intnet-attack | 10.10.40.0/24 | 10.10.40.1 | Static | none | Untrusted | Blocked (recommended) |
| MGMT | vboxnet0 (host-only) | 10.10.99.0/24 | 10.10.99.2 (OPNsense) / 10.10.99.1 (host) | Static | none | Trusted | none |

## IP allocation convention

- `.1` gateway · `.10-.19` infrastructure/servers · `.20-.29` secondary servers/workstations · `.100+` reserved for temporary test VMs
- Every IP in use must appear in `hosts.md`. Unlisted IP seen on a segment = investigate (good detection exercise).

## DNS

| Zone | Authoritative server | Notes |
|---|---|---|
| lab.local | corp-dc01 (10.10.20.10) | AD-integrated DNS |
| everything else | OPNsense Unbound or upstream | forward to public resolver |
