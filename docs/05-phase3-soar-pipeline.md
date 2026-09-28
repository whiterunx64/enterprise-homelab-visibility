# 05 — Phase 3: AI-Powered SOAR Pipeline (Wazuh + n8n + Gemini)

**Goal:** Wazuh alerts trigger an n8n workflow that classifies severity, generates a plain-English explanation and recommended response via Gemini, then emails the analyst a formatted alert — scoped by severity (e.g., High = immediate email, Low = digest/log only).

This is the centerpiece of the portfolio project — the "instead of just forwarding raw alerts" pitch.

## Architecture of the pipeline

```
Wazuh Manager (alert fires)
   → HTTP POST to n8n webhook
   → n8n workflow:
        1. Parse Wazuh alert JSON
        2. Call Gemini API: classify severity + explain + recommend action
        3. Branch on severity (High/Medium/Low)
        4. Format email (HTML) per severity template
        5. Send via Gmail node
   → Analyst inbox
```

## 1. Deploy n8n

```bash
# On soc-n8n (Ubuntu Server + Docker)
docker volume create n8n_data
docker run -d --name n8n -p 5678:5678 \
  -v n8n_data:/home/node/.n8n \
  -e N8N_HOST=10.10.30.30 \
  -e WEBHOOK_URL=http://10.10.30.30:5678/ \
  n8nio/n8n
```

Access the n8n editor at `http://10.10.30.30:5678`.

## 2. Create the webhook trigger

- New workflow → add a **Webhook** node, method POST, path e.g. `/wazuh-alert`
- Copy the generated webhook URL (test and production versions)

## 3. Configure Wazuh to call the webhook

In `/var/ossec/etc/ossec.conf` on `soc-wazuh-mgr`, add an integration block:

```xml
<ossec_config>
  <integration>
    <name>custom-n8n</name>
    <hook_url>http://10.10.30.30:5678/webhook/wazuh-alert</hook_url>
    <level>7</level>
    <alert_format>json</alert_format>
  </integration>
</ossec_config>
```

Adjust `<level>` to the minimum Wazuh rule level you want forwarded (7+ is a reasonable "worth triaging" floor — tune based on noise). Restart the manager: `systemctl restart wazuh-manager`.

## 4. n8n workflow: parse + classify

- **Function/Set node:** extract fields you care about from the Wazuh JSON payload — `rule.description`, `rule.level`, `agent.name`, `data.srcip`, `full_log`, timestamp.
- **HTTP Request node → Gemini API:** send a prompt built from the extracted fields. Example prompt structure:

```
You are a SOC triage assistant. Given this security alert, respond ONLY with JSON:
{
  "severity": "High|Medium|Low",
  "explanation": "plain-English summary a non-security manager could understand",
  "recommended_action": "concrete next step for the analyst"
}

Alert:
Rule: {{rule.description}}
Level: {{rule.level}}
Source IP: {{data.srcip}}
Agent: {{agent.name}}
Raw log: {{full_log}}
```

- **Parse Gemini's JSON response** with a Set/Code node (strip markdown fences defensively, same as the artifact-building pattern — LLM outputs sometimes wrap JSON in ```json).

## 5. Branch on severity + send email

- **Switch/IF node** on the parsed `severity` field
- **High:** immediate email, subject prefixed `🔴 HIGH`, sent via Gmail node
- **Medium:** email, subject prefixed `🟠 MEDIUM`
- **Low:** log to a running digest (e.g., append to a Google Sheet or just log in n8n) rather than emailing every time — this is a good design point to call out in your writeup: not every alert should page a human

## 6. Gmail node setup

- Use a dedicated Gmail account for lab alerting (not your personal one)
- Authenticate via OAuth2 credential in n8n (Gmail node → Create New Credential)
- Build an HTML email template per severity with the fields from step 4

## Security notes for this component specifically

- Store the Gemini API key and Gmail OAuth credentials in n8n's credential store, never hardcoded in workflow JSON that gets committed to git
- Add `.env`, credential exports, and any n8n workflow JSON containing embedded secrets to `.gitignore`
- If n8n needs outbound internet (Gemini + Gmail), the OPNsense SOC-interface rule allowing HTTPS/443 outbound (from `03-phase1-opnsense.md`) covers this — don't open SOC to broader outbound access than that

## Validated scenario (already proven per your notes)

SSH brute-force against a monitored host → Wazuh rule fires (level ~10, `sshd: authentication failed` pattern) → webhook → Gemini classifies High → email sent within seconds. Use this as your first end-to-end validation before adding more scenario types in `09-attack-scenarios.md`.

## Verification

- [ ] Manually POST a sample Wazuh alert JSON to the webhook — email arrives correctly formatted
- [ ] Real SSH brute-force test triggers the full chain end-to-end
- [ ] Severity branching confirmed (force a Low-level alert and confirm it does NOT trigger an email, only a log entry)
- [ ] Email content is human-readable, not raw JSON dumped into the body

Next: `06-phase4-windows-domain.md`
