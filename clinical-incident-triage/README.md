# 🏥 Clinical Incident Triage Automation

> **AI-powered incident reporting system for healthcare facilities — built on Make.com**  
> Zero manual escalation. Real-time severity classification. Full audit trail. Built in 2 hours.

---

## The Problem

A nurse witnesses a patient fall at 2am.

She fills out a paper form. Puts it in a tray. Hopes someone reads it by Monday.

The director finds out 3 days later. The compliance deadline is already missed.

This is not a rare failure. **This is Tuesday in most healthcare facilities.**

Clinical incident reporting is broken:
- Paper forms or basic emails that sit unread
- No automatic escalation based on severity
- Managers find out too late
- No audit trail
- Staff who report incidents get zero acknowledgment

---

## The Solution

A staff member submits a Google Form → **AI instantly classifies severity** → the right people are alerted across Email, Slack, and SMS → the reporter gets an auto-acknowledgment with a reference number → a live dashboard updates automatically.

**All within 47 seconds. Zero manual effort.**

---

## System Architecture

```
Google Forms (Incident Submitted)
        │
        ▼
Google Sheets Trigger (Watch New Rows)
        │
        ▼
OpenAI GPT-4o (AI Severity Classification)
  → Reads incident description
  → Classifies: CRITICAL / HIGH / MEDIUM / LOW
  → Writes executive summary
  → Recommends immediate actions
        │
        ▼
Tools Module (Generate Reference ID: INC-YYYYMMDD-HHMMSS)
        │
        ▼
Google Sheets (Log Incident to Dashboard)
        │
        ▼
Router (Severity-Based Branching)
  ├── CRITICAL → Email Director + Slack #critical-incidents + SMS on-call manager
  ├── HIGH     → Email Department Head + Slack #incidents
  ├── MEDIUM   → Email Supervisor
  └── LOW      → Log only (fallback route)
        │
        ▼
Gmail (Auto-Acknowledgment to Reporter)
        │
        ▼
Google Sheets (Update Status + Reference ID)
```

---

## Features

| Feature | Description |
|---|---|
| 🤖 AI Triage | GPT-4o reads the incident description and classifies severity — not a checkbox, actual intelligence |
| ⚡ Instant Routing | CRITICAL incidents reach the director, Slack, and on-call manager simultaneously |
| 📧 Auto-Acknowledgment | Reporter receives confirmation + unique reference number within seconds |
| 📊 Live Dashboard | Google Sheets dashboard with timestamp, AI classification, and audit trail |
| 🔖 Reference IDs | Every incident gets a traceable ID: `INC-20260507-023015` |
| 🔄 24/7 Operation | Runs every 15 minutes automatically, no human required |

---

## Tech Stack

| Tool | Role |
|---|---|
| [Make.com](https://make.com) | Automation orchestration |
| Google Forms | Staff-facing incident submission |
| Google Sheets | Live dashboard and audit trail |
| OpenAI GPT-4o | Severity classification and summary generation |
| Gmail | Email alerts and acknowledgments |
| Slack | Real-time team notifications |

---

## Getting Started

### Prerequisites

- [Make.com](https://make.com) account (free tier works)
- Google account (Forms + Sheets + Gmail)
- OpenAI API key with GPT-4o access ([platform.openai.com](https://platform.openai.com))
- Slack workspace (optional but recommended)

### Setup Guide

See the full step-by-step setup in [`docs/SETUP.md`](docs/SETUP.md)

**Quick overview:**
1. Create the Google Form using the field structure in [`docs/FORM_STRUCTURE.md`](docs/FORM_STRUCTURE.md)
2. Link the Form to Google Sheets (auto-creates the responses tab)
3. Import the Make.com blueprint from [`blueprint/`](blueprint/)
4. Add your API credentials in Make.com
5. Run once with a test submission
6. Activate the schedule

---

## Google Sheets Dashboard

The live tracker includes these columns:

| Column | Source |
|---|---|
| Timestamp | Google Forms auto |
| Email Address | Reporter (Forms) |
| Reporter Name | Reporter (Forms) |
| Department | Reporter (Forms) |
| Date & Time of Incident | Reporter (Forms) |
| Incident Type | Reporter (Forms) |
| Was Patient Harmed | Reporter (Forms) |
| Immediate Action Taken | Reporter (Forms) |
| Description | Reporter (Forms) |
| AI Severity | OpenAI classification |
| AI Summary | OpenAI executive summary |
| AI Recommended Actions | OpenAI suggested actions |
| Status | Make.com (Open / In Progress / Closed) |
| Reference ID | Make.com generated (INC-YYYYMMDD-HHMMSS) |

---

## OpenAI Prompt

The system prompt used for classification:

```
You are a clinical incident triage assistant for an NHS healthcare facility.

Analyse the incident and respond in EXACTLY this format, nothing else:

SEVERITY: [write only one word: CRITICAL or HIGH or MEDIUM or LOW]
SUMMARY: [one sentence summary]
ACTIONS: [action 1]; [action 2]; [action 3]

Severity rules:
- CRITICAL: patient death, serious injury, ongoing immediate risk
- HIGH: patient harmed, significant risk, regulatory reportable
- MEDIUM: near-miss, minor harm, equipment issue
- LOW: no harm, process issue, documentation concern
```

---

## Escalation Matrix

| Severity | Who Gets Alerted | Channels |
|---|---|---|
| 🔴 CRITICAL | Director + On-call Manager | Email + Slack + SMS |
| 🟠 HIGH | Department Head | Email + Slack |
| 🟡 MEDIUM | Supervisor | Email |
| 🟢 LOW | Logged only | Dashboard |

---

## Make.com Blueprint

The scenario blueprint is exported in [`blueprint/scenario-blueprint.json`](blueprint/scenario-blueprint.json).

To import it into Make.com:
1. Open Make.com → Create new scenario
2. Click the **three dots** menu (top right of canvas)
3. Select **Import Blueprint**
4. Upload `scenario-blueprint.json`
5. Reconnect your credentials (Google, OpenAI, Gmail, Slack)

> ⚠️ You will need to reconnect all service credentials after importing — Make.com does not export API keys for security reasons.

---

## Cost

| Service | Cost |
|---|---|
| Make.com Free tier | 1,000 operations/month — ~110 incidents |
| Make.com Core (£9/mo) | 10,000 operations/month — ~1,100 incidents |
| OpenAI GPT-4o | ~$0.001 per incident classification |
| Google Workspace | Free |

**Total cost for a small clinic: under £10/month.**

---

## Compliance Notes

- All incident data is stored in your own Google Sheets — you control the data
- No patient identifiable information (PII) is required in the form
- Reference IDs provide a traceable audit trail for CQC inspections
- Recommended: restrict Google Sheet access to clinical governance team only

---

## Roadmap

- [ ] WhatsApp alerts via Twilio
- [ ] Weekly automated summary report (PDF via email)
- [ ] Power BI / Looker Studio dashboard integration
- [ ] DATIX integration for NHS trusts
- [ ] Escalation reminder if CRITICAL incident unacknowledged after 30 minutes
- [ ] Multi-site support (filter by facility)

---

## Author

Built by **King Onwuka**  
MSc Computing — Sheffield Hallam University  
Founder — [Willow Companions](https://willowcompanions.co.uk)  
Focus: AI automation in healthcare and elderly care systems

---

## Licence

MIT — free to use, adapt, and deploy in your own healthcare facility.

---

> *"This took 2 hours to build. It could save lives."*
