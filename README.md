# AI-Powered SOC Alert Triage Engine

> Automated SOC alert pipeline built on Tines SOAR — ingests Splunk alerts, enriches across multiple threat intel sources, maps to MITRE ATT&CK, generates AI-assisted analyst summaries, and auto-routes by severity.

---

## Overview

This project implements a fully automated SOC alert triage workflow using Tines SOAR. A raw Splunk alert enters via webhook and exits as a structured, analyst-ready incident report — delivered to the right person at the right severity level — in under 30 seconds, with zero manual intervention.

**Reduces manual triage time from ~15 minutes to under 30 seconds per alert.**

---

## Architecture

```
Splunk Alert (webhook)
        |
        ├── AbuseIPDB enrichment  (abuse score, ISP, country, reports)
        ├── VirusTotal enrichment (malicious vendor detections)
        └── Shodan enrichment     (open ports, org, vulnerabilities)
                |
        Event Transform (merge all 3 sources)
                |
        MITRE ATT&CK Mapping (technique ID, name, tactic)
                |
        AI Analysis (Claude/OpenAI — verdict, summary, recommendation)
                |
        Severity Condition (abuse score threshold)
                |
        ├── HIGH  → 🚨 Full incident report emailed immediately
        └── LOW   → ⚠️  Summary email for analyst review
```

---

## Features

- **Multi-source threat enrichment** — AbuseIPDB, VirusTotal, and Shodan queried in parallel
- **MITRE ATT&CK mapping** — automatic technique ID and tactic tagging based on alert type
- **AI-assisted analysis** — generates severity verdict, incident summary, and analyst recommendation
- **Automated severity routing** — high risk alerts paged immediately, low risk logged for review
- **Production-safe credentials** — API keys stored in Tines credential store, never hardcoded
- **Always-on** — runs 24/7 without manual script execution

---

## Tech Stack

| Component | Tool |
|---|---|
| SOAR Platform | Tines (Community Edition) |
| SIEM | Splunk |
| Threat Intel | AbuseIPDB · VirusTotal · Shodan |
| AI Analysis | OpenAI GPT / Anthropic Claude |
| Framework | MITRE ATT&CK |
| Language | Tines Liquid templating |

---

## MITRE ATT&CK Coverage

| Alert Type | Technique ID | Tactic |
|---|---|---|
| Brute Force | T1110 | Credential Access |
| PowerShell Abuse | T1059.001 | Execution |
| DNS Beaconing | T1071.004 | Command and Control |
| Suspicious Process | T1055 | Defense Evasion |
| Lateral Movement | T1021 | Lateral Movement |

---

## Sample AI-Generated Incident Report

```
SEVERITY VERDICT: HIGH

SUMMARY: IP 185.220.101.45 is a known Tor exit node with an abuse
confidence score of 100/100 and 99 community reports. The brute
force activity targeting WIN-DC01 via Event ID 4625 aligns with
MITRE T1110 Credential Access tactics.

RECOMMENDATION: Immediately block IP 185.220.101.45 at the firewall,
reset credentials on WIN-DC01, and investigate authentication logs
for successful logins from this IP.

CONFIDENCE: HIGH because abuse score is maximum with extensive
community reporting and Tor exit node classification confirms
malicious infrastructure.
```

---

## Setup

### Prerequisites
- Tines account (free community edition at tines.com)
- Splunk instance with saved alert rules
- API keys: AbuseIPDB · VirusTotal · Shodan · OpenAI or Anthropic

### Installation

1. Clone this repository
```bash
git clone https://github.com/B844-max/soc-alert-triage-engine
```

2. Import the story into Tines
   - Open Tines → New Story → Import
   - Upload `soc_triage_engine.json`

3. Add your credentials in Tines
   - Settings → Credentials → add each API key

4. Configure Splunk webhook
   - Splunk → Alert → Add Actions → Webhook
   - Paste your Tines webhook URL

5. Test with a sample payload
```powershell
$body = '{"src_ip":"185.220.101.45","alert_name":"Brute Force","severity":"high","host":"WIN-DC01","event_id":"4625"}'
Invoke-WebRequest -Uri "YOUR_WEBHOOK_URL" -Method POST -ContentType "application/json" -Body $body
```

---

## Project Background

Built as part of my SOC automation learning path. This project extends my earlier Python-based alert enrichment pipeline (AbuseIPDB + AI analysis) into a production-grade SOAR workflow on Tines, adding VirusTotal and Shodan enrichment, MITRE ATT&CK mapping, and automated severity-based routing.

---

## Author

**Hir Doshi**  
B.Tech Computer Science — Pandit Deendayal Energy University  
[LinkedIn](https://linkedin.com/in/hir-doshi-6a0475273) · [GitHub](https://github.com/B844-max)
