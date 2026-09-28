# Change Log

Dated record of every infrastructure change. Doubles as your build journal and a source for LinkedIn posts. Newest entries at top.

| Date | ID | Host(s) | Change | Reason | Rollback | Verified by | Commit |
|---|---|---|---|---|---|---|---|
| YYYY-MM-DD | CHG-001 | fw-opnsense | Initial install, interfaces assigned | Phase 1 start | Delete VM | Ping from each segment | |

## Run profiles

| Profile | VMs running | Approx RAM | Use case |
|---|---|---|---|
| Core | fw, securityonion, wazuh-mgr, n8n | ~16GB | Daily SOC/SOAR development |
| Exercise | Core + dmz-web01, dmz-meta, atk-kali | ~23GB | Attack scenarios |
| Full | All | ~31GB | Lateral movement / AD scenarios |

## Snapshot naming convention

`<hostname>-<phase>-<YYYYMMDD>-<state>` e.g. `fw-opnsense-p1-20260930-baseline`. Take a snapshot at the end of every phase before proceeding.

## Commit message convention

`inventory: <what changed> (CHG-###)` — e.g. `inventory: add dmz-web01 host record (CHG-014)`.
