# Setup Guide

Complete step-by-step instructions to deploy the Clinical Incident Triage Automation system.

---

## Step 1 — Create the Google Form

Go to [forms.google.com](https://forms.google.com) → Blank form  
Title: `Clinical Incident Report`

Add fields in this exact order:

| Field Label | Field Type | Required |
|---|---|---|
| Reporter name | Short answer | Yes |
| Department | Dropdown | Yes |
| Date & time of incident | Date (include time) | Yes |
| Incident type | Dropdown | Yes |
| Describe what happened | Paragraph | Yes |
| Was the patient harmed? | Multiple choice | Yes |
| Immediate action taken | Paragraph | Yes |
| Your email address | Short answer | Yes |

**Department dropdown options:**
A&E, Ward, ICU, Pharmacy, Outpatients, Other

**Incident type dropdown options:**
Patient fall, Medication error, Equipment failure, Near-miss, Infection risk, Other

**Patient harmed options:**
Yes, No, Unsure

**Settings:** Click Settings tab → turn on **Collect email addresses** → Save

---

## Step 2 — Link Form to Google Sheets

1. In your Google Form, click the **Responses** tab
2. Click the green **Sheets** icon (Create Spreadsheet)
3. Name it: `Incident Tracker`
4. Click **Create**

Google will auto-generate a `Form Responses 1` tab with clean column headers matching your form fields. This is the sheet Make.com will watch.

---

## Step 3 — Add Extra Columns to the Sheet

In the `Form Responses 1` tab, add these columns to the right of the existing ones (Google Forms fills columns A-I, so start from J):

| Column | Header |
|---|---|
| J | AI Severity |
| K | AI Summary |
| L | AI Recommended Actions |
| M | Status |
| N | Reference ID |

---

## Step 4 — Get Your OpenAI API Key

1. Go to [platform.openai.com](https://platform.openai.com)
2. Click **API Keys** in the left menu
3. Click **Create new secret key**
4. Copy and save it — you will not see it again

> Minimum $5 credit required. Cost per incident is approximately $0.001.

---

## Step 5 — Set Up Make.com

### Option A — Import the Blueprint (Recommended)

1. Go to [make.com](https://make.com) → Create new scenario
2. Click the **...** menu (top right of the canvas)
3. Select **Import Blueprint**
4. Upload `blueprint/scenario-blueprint.json` from this repo
5. Follow Step 6 to reconnect credentials

### Option B — Build Manually

Follow the module-by-module instructions below.

---

## Step 6 — Build the Scenario Manually

### Module 1: Google Sheets — Watch New Rows

- App: Google Sheets
- Action: Watch New Rows
- Spreadsheet: Incident Tracker
- Sheet: Form Responses 1
- Limit: 1
- Where to start: From now on

### Module 2: OpenAI — Generate a Completion

- App: OpenAI (ChatGPT, Sora, Whisper)
- Action: Create a Completion
- Model: gpt-4o
- Max Tokens: 300

**System message:**
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

**User message (map from Module 1 columns):**
```
Incident Type: [Column F — Incident type]
Patient Harmed: [Column G — Was the patient harmed?]
Description: [Column I — Describe what happened]
Immediate Action: [Column H — Immediate action taken]
Department: [Column D — Department]
```

### Module 3: Tools — Set Variable

- Variable name: `reference_id`
- Variable value: `INC-{{formatDate(now; "YYYYMMDD-HHmmss")}}`

### Module 4: Google Sheets — Add a Row

Write AI results back to the tracker. Map each column:

| Sheet Column | Value |
|---|---|
| J (AI Severity) | `2. Result` from OpenAI |
| K (AI Summary) | `2. Result` from OpenAI |
| L (AI Recommended Actions) | `2. Result` from OpenAI |
| M (Status) | Type: `Open` |
| N (Reference ID) | `reference_id` variable from Module 3 |

### Module 5: Router

Add 4 routes:

**Route 1 — CRITICAL**
- Label: CRITICAL incidents
- Condition: `2. Result` → Contains → `SEVERITY: CRITICAL`

**Route 2 — HIGH**
- Condition: `2. Result` → Contains → `SEVERITY: HIGH`

**Route 3 — MEDIUM**
- Condition: `2. Result` → Contains → `SEVERITY: MEDIUM`

**Route 4 — LOW**
- Set as Fallback route: Yes

### Module 6 (CRITICAL route): Gmail — Send Email

- To: director@yourclinic.com
- Subject: `🔴 CRITICAL INCIDENT — [reference_id] — Immediate Action Required`
- Body: Include department, incident type, AI summary, AI recommended actions

### Module 7 (CRITICAL route): Slack — Post Message

- Channel: #critical-incidents
- Message: Include severity, reference ID, department, AI summary

### Module 8 (HIGH route): Gmail — Send Email

- To: depthead@yourclinic.com
- Subject: `🟠 HIGH Severity Incident — [reference_id]`

### Module 9 (MEDIUM route): Gmail — Send Email

- To: supervisor@yourclinic.com
- Subject: `🟡 Incident Logged — [reference_id]`

### Module 10 (all routes): Gmail — Auto-Acknowledgment

- To: Column B (reporter email from form)
- Subject: `✅ Incident Report Received — Reference: [reference_id]`
- Body: Confirmation message with reference number

---

## Step 7 — Test the System

1. Click **Run once** in Make.com
2. Open your Google Form in a new tab
3. Submit a test entry:
   - Incident type: Patient fall
   - Patient harmed: Yes
   - Description: Patient found on floor, reporting hip pain, bed rails were down
   - Department: ICU
4. Watch Make.com execute — each module should turn green
5. Check that:
   - [ ] New row appeared in Google Sheets with AI columns filled
   - [ ] Alert email arrived at the correct address
   - [ ] Slack message posted (if configured)
   - [ ] Acknowledgment email arrived at the reporter email

---

## Step 8 — Activate the Schedule

Toggle **Every 15 minutes** at the bottom of the Make.com canvas.

For real-time triggering (instant, not 15-minute delay), upgrade to Make.com Core and switch the trigger to a **Webhook**.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Everything goes to LOW severity | OpenAI is not receiving incident description — check Module 2 user message mapping |
| Variable not showing as coloured pill | Delete typed text, click field, select from dropdown picker — never type variables manually |
| 50,000 character limit error | A cell is being mapped to an entire object — open Google Sheets module, find and fix oversized mapping |
| Emails not sending | Gmail modules reference a deleted module — remap all broken variables to `2. Result` |
| JSON parse error | Remove the JSON Parse module entirely — use plain text OpenAI output with Contains filter |
