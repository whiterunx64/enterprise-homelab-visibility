# Credentials & Secrets Policy

**This repo contains zero secrets.** Public GitHub + portfolio = anything committed is compromised.

## Where secrets live

| Secret | Storage |
|---|---|
| Host/VM admin passwords | Password manager (e.g., Bitwarden/KeePassXC), entry named `homelab/<hostname>` |
| Wazuh generated admin creds | Password manager |
| Gemini API key | n8n credential store only |
| Gmail OAuth | n8n credential store only |
| SSH keys | `~/.ssh/` on your workstation; only public keys are referenced |

## Rules

1. Lab-only default creds (DVWA `admin/password`, Metasploitable `msfadmin`) are public knowledge and may be documented as *intentional vulnerabilities*, never reused elsewhere.
2. Use unique passwords per host even in the lab, so credential-reuse detection scenarios are meaningful.
3. Before every push: `git diff --staged | grep -iE "password|apikey|secret|token"`
4. Rotate the Gemini key and Gmail OAuth if an export is ever committed by mistake, and rewrite history (`git filter-repo`) rather than just deleting the file.
5. Screenshots: crop/blur API keys, emails, and any real public IP.

## Optional hardening

Add a pre-commit hook using `gitleaks` to block secret commits automatically.
