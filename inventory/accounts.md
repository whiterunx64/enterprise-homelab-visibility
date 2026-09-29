# Account Register

Every local/domain account that exists on any lab host — username, what it's for, how privileged it is, and its password. This is the single place to check "does this account exist, and what can it do" for any host in the lab.

## Register

| Host | Username | Account type | Purpose | Privilege | Password | MFA | Created | Last rotated | Status |
|---|---|---|---|---|---|---|---|---|---|
| fw-opnsense | admin | Local (web UI) | Firewall management | Admin | `Fw!Opn2026#Lab9` | n/a | | | planned |
| soc-securityonion | onionuser | Local | Console/SSH access | Admin | `S0c!Onion2026#Lab` | n/a | | | planned |
| soc-wazuh-mgr | admin | Local (Wazuh dashboard) | SIEM dashboard access | Admin | `Wzh!Dash2026#Mgr7` | n/a | | | planned |
| soc-wazuh-mgr | wazuh-lab | Linux system account | SSH/ops | sudo | `Wzh!Sys2026#Ops3` | n/a | | | planned |
| soc-n8n | owner | Local (n8n UI) | Workflow editor access | Admin | `N8n!Flow2026#Soc5` | n/a | | | planned |
| corp-dc01 | lab\administrator | Domain (Built-in) | AD/domain admin | Domain Admin | `Corp!DC2026#Adm1n` | n/a | | | planned |
| corp-dc01 | lab\analyst1 | Domain (standard user) | Simulated normal user for detection scenarios | Standard | `Corp!Usr2026#Ana2` | n/a | | | planned |
| corp-dc01 | lab\svc-wazuh | Domain (service account) | Wazuh AD integration, if used | Least-priv | `Corp!Svc2026#Wzh4` | n/a | | | planned |
| corp-win10 | local-admin | Local | Fallback console access | Admin | `Win10!Loc2026#Adm` | n/a | | | planned |
| dmz-web01 | lab | Local | SSH/ops | sudo | `Dmz!Web2026#Ssh6` | n/a | | | planned |
| dmz-web01 | admin | DVWA app login | Intentional vulnerability | n/a | `password` | n/a | | | planned |
| dmz-meta | msfadmin | Local | Intentional vulnerability (Metasploitable default) | n/a | `msfadmin` | n/a | | | planned |
| atk-kali | kali | Local | Attacker console | Admin | `Kali!Atk2026#Rt8` | n/a | | | planned |

## How to apply each account

### fw-opnsense — admin
Set during initial install/console wizard, or change later:
System → Access → Users → edit `admin` → set password to match the table.

### soc-securityonion — onionuser
Set during the Security Onion setup wizard. To change later, on the console:
```
sudo passwd onionuser
```

### soc-wazuh-mgr — admin (Wazuh dashboard)
Generated at install time by `wazuh-install.sh -a`. To set it to the table value:
```
sudo /usr/share/wazuh-indexer/bin/wazuh-passwords-tool.sh --change-password -u admin -p 'Wzh!Dash2026#Mgr7'
```

### soc-wazuh-mgr — wazuh-lab (Linux system account)
```
sudo adduser wazuh-lab
sudo usermod -aG sudo wazuh-lab
sudo passwd wazuh-lab
```

### soc-n8n — owner
Created on n8n's first launch (the setup screen it shows the first time you open the web UI at `:5678`). Enter this email/username and password there — it isn't set via CLI.

### corp-dc01 — lab\administrator
This is the built-in account; you set its password during the Windows Server install itself (the "Administrator password" screen), or later:
```powershell
net user administrator "Corp!DC2026#Adm1n"
```

### corp-dc01 — lab\analyst1 (standard user)
Run on the DC after AD DS is promoted, in PowerShell:
```powershell
New-ADUser -Name "analyst1" -SamAccountName "analyst1" `
  -AccountPassword (ConvertTo-SecureString "Corp!Usr2026#Ana2" -AsPlainText -Force) `
  -Enabled $true
```

### corp-dc01 — lab\svc-wazuh (service account)
```powershell
New-ADUser -Name "svc-wazuh" -SamAccountName "svc-wazuh" `
  -AccountPassword (ConvertTo-SecureString "Corp!Svc2026#Wzh4" -AsPlainText -Force) `
  -Enabled $true -PasswordNeverExpires $true
```

### corp-win10 — local-admin
Create during the Windows 10/11 setup ("Who's going to use this device" screen), or after install:
```powershell
net user local-admin "Win10!Loc2026#Adm" /add
net localgroup Administrators local-admin /add
```

### dmz-web01 — lab (SSH/ops)
```
sudo adduser lab
sudo usermod -aG sudo lab
```

### dmz-web01 — admin (DVWA login)
Nothing to create — this is DVWA's built-in default app login (`admin` / `password`). It exists as soon as the DVWA container is running; that's the intentional vulnerability.

### dmz-meta — msfadmin
Nothing to create — this account ships pre-built into the Metasploitable image by design.

### atk-kali — kali
Default account on the Kali installer image. If you kept the installer default password, change it to match the table:
```
passwd kali
```

## Rules

1. **`dmz-web01` (DVWA login) and `dmz-meta` (`msfadmin`) are intentional, publicly documented default credentials** — that's the point of those two hosts, they're deliberately vulnerable targets. Never reuse those exact strings on any other host in the lab.
2. Every other account should have a unique password — reusing one password across hosts defeats the purpose of a lab meant to demonstrate good practice.
3. Rotate infrastructure account passwords (`fw-opnsense`, the `soc-*` hosts, domain admin) at the start of each new build phase, and update the "Last rotated" column when you do.
4. When you decommission a host, mark every account on it `decommissioned` the same day — don't leave stale rows.
5. For domain accounts, double check the AD group membership actually matches what "Purpose" says — this table should never drift from what's really configured.
