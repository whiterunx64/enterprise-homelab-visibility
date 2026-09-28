# 09 — Attack Scenarios (MITRE ATT&CK-Mapped)

Each scenario: technique, ATT&CK ID, Kali command, expected detection source, expected SOAR outcome.

## 1. SSH Brute-Force (validated baseline)

- **ATT&CK:** T1110.001 (Brute Force: Password Guessing)
- **Command:** `hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://10.10.10.10`
- **Expected detection:** Wazuh rule for repeated `sshd` auth failures (level ~10); Suricata may also flag high-volume connection attempts
- **Expected SOAR outcome:** High severity → immediate email with source IP, target, and recommended action (block source IP at firewall, force credential rotation)

## 2. Network/Service Scanning

- **ATT&CK:** T1046 (Network Service Discovery)
- **Command:** `nmap -sS -sV -p- 10.10.10.0/24`
- **Expected detection:** Suricata ET SCAN signatures fire on Security Onion; Zeek connection logs show scan pattern (many ports, one source, short duration)
- **Expected SOAR outcome:** Medium severity typically — scanning alone isn't compromise, good scenario to show your severity logic isn't just "everything is High"

## 3. Web Application Exploitation

- **ATT&CK:** T1190 (Exploit Public-Facing Application)
- **Target:** DVWA or Juice Shop on `dmz-web01`
- **Example:** SQLi via `sqlmap -u "http://10.10.10.10/vulnerabilities/sqli/?id=1" --batch --dbs`
- **Expected detection:** Suricata HTTP/SQLi signatures; if Wazuh agent installed on the target, web server log-based rules too
- **Expected SOAR outcome:** High severity, explanation should name the technique (SQL injection) and recommend WAF/input validation review

## 4. Lateral Movement Attempt (containment test)

- **ATT&CK:** T1021 (Remote Services) / T1210 (Exploitation of Remote Services)
- **Setup:** Temporarily open a narrow ATTACK → CORP rule on OPNsense (document the exact rule, then close it after)
- **Command:** `crackmapexec smb 10.10.20.0/24 -u admin -p 'Password123'` (or similar) from a simulated "already compromised DMZ host" perspective — for realism, run this *from a DMZ box* with a temporary DMZ→CORP allow rule rather than directly from Kali, to test actual segment containment
- **Expected detection:** Wazuh/Sysmon on `corp-win10` or `corp-dc01` shows SMB auth attempts; OPNsense logs the rule hit
- **Expected SOAR outcome:** High severity, and — critically — document that with the rule *closed* (default state), this attack fails outright. That contrast (contained vs. temporarily-opened) is your strongest architecture-validation evidence.

## 5. Active Directory Attack Path (optional stretch goal)

- **ATT&CK:** T1558 (Steal or Forge Kerberos Tickets) or T1087 (Account Discovery)
- **Tooling:** BloodHound for AD enumeration, `netexec`/`crackmapexec` for auth testing
- **Expected detection:** Sysmon + Wazuh AD-specific rules (if using the Wazuh AD ruleset) on `corp-dc01`
- **Expected SOAR outcome:** High severity, recommended action should reference credential rotation / Kerberos ticket invalidation

## Recording results

Use the evidence table format from `08-phase6-kali-attacks.md`. Screenshot each: (1) the Kali command run, (2) the Security Onion/Wazuh alert, (3) the resulting SOAR email. These three-shot sets are your best portfolio/LinkedIn content per scenario.

Next: `10-validation-checklist.md`
