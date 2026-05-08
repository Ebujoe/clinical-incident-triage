# Google Form Structure

The incident report form is the staff-facing entry point. Keep it simple — staff are often under pressure when reporting.

---

## Form Title

`Clinical Incident Report`

## Form Description

```
Use this form to report any clinical incident, near-miss, or patient safety concern.
Your report is received immediately by the clinical governance team.
All submissions are confidential and will be acknowledged within minutes.
```

---

## Fields

### Field 1 — Reporter Name
- Type: Short answer
- Label: `Reporter name`
- Required: Yes

### Field 2 — Department
- Type: Dropdown
- Label: `Department`
- Required: Yes
- Options:
  - A&E
  - Ward
  - ICU
  - Pharmacy
  - Outpatients
  - Theatre
  - Radiology
  - Other

### Field 3 — Date & Time of Incident
- Type: Date (with time enabled)
- Label: `Date & time of incident`
- Required: Yes

### Field 4 — Incident Type
- Type: Dropdown
- Label: `Incident type`
- Required: Yes
- Options:
  - Patient fall
  - Medication error
  - Equipment failure
  - Near-miss
  - Infection risk
  - Pressure injury
  - Verbal/physical aggression
  - Unexpected deterioration
  - Documentation error
  - Other

### Field 5 — Description
- Type: Paragraph (long answer)
- Label: `Describe what happened`
- Required: Yes
- Description hint: `Please include: what happened, where, who was involved, and any contributing factors you observed`

### Field 6 — Patient Harmed
- Type: Multiple choice
- Label: `Was the patient harmed?`
- Required: Yes
- Options:
  - Yes
  - No
  - Unsure

### Field 7 — Immediate Action Taken
- Type: Paragraph (long answer)
- Label: `Immediate action taken`
- Required: Yes
- Description hint: `What did you or your colleagues do immediately after the incident?`

### Field 8 — Reporter Email
- Type: Short answer
- Label: `Your email address`
- Required: Yes
- Validation: Email format

---

## Form Settings

- **Collect email addresses:** On (for acknowledgment routing)
- **Limit to 1 response:** Off (staff may need to report multiple incidents)
- **Show progress bar:** Off
- **Confirmation message:** 
  ```
  Thank you for submitting your report. You will receive an email acknowledgment with your reference number shortly. If this is a life-threatening emergency, call 999 immediately.
  ```

---

## Google Sheets Column Mapping

When linked to Google Sheets, Google Forms auto-creates these columns:

| Column | Header |
|---|---|
| A | Timestamp |
| B | Email Address |
| C | Reporter name |
| D | Department |
| E | Date & time of incident |
| F | Incident type |
| G | Was the patient harmed? |
| H | Immediate action taken |
| I | Describe what happened |

> Note: Column order may vary based on your exact form field order. Always verify column letters in Make.com after the first test run.
