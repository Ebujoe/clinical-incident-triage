# OpenAI Prompts

The AI triage system uses GPT-4o to classify severity, summarise the incident, and recommend immediate actions.

---

## System Prompt

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

## User Prompt Template

```
Incident Type: {incident_type}
Patient Harmed: {patient_harmed}
Description: {description}
Immediate Action: {immediate_action}
Department: {department}
```

---

## Example Outputs

### CRITICAL
**Input:** Patient unresponsive in Ward 3, CPR in progress, no defibrillator available  
**Output:**
```
SEVERITY: CRITICAL
SUMMARY: Patient found unresponsive in Ward 3 with CPR in progress and no defibrillator available.
ACTIONS: Call cardiac arrest team immediately; Source nearest defibrillator; Initiate incident investigation
```

### HIGH
**Input:** Patient fell from ICU bed, reports hip pain, bed rails were lowered  
**Output:**
```
SEVERITY: HIGH
SUMMARY: Patient fell from ICU bed with possible hip injury due to lowered bed rails.
ACTIONS: Arrange urgent X-ray for hip assessment; Review bed rail policy; Complete regulatory report within 24 hours
```

### MEDIUM
**Input:** Wrong medication dose nearly administered, caught by second nurse before giving  
**Output:**
```
SEVERITY: MEDIUM
SUMMARY: Near-miss medication dosage error prevented by double-check procedure.
ACTIONS: Review medication administration protocol; Brief ward team on error type; Document in pharmacy system
```

### LOW
**Input:** Patient call bell was out of reach, patient had to call verbally for 5 minutes  
**Output:**
```
SEVERITY: LOW
SUMMARY: Call bell inaccessibility caused minor delay in patient response.
ACTIONS: Remind staff to check call bell placement at each round; Log for process review; No immediate risk
```

---

## Model Configuration

| Parameter | Value | Reason |
|---|---|---|
| Model | gpt-4o | Best reasoning for clinical context |
| Max tokens | 300 | Sufficient for 3-line response, prevents verbose output |
| Temperature | 1 (default) | Standard — adjust lower (0.3) for more deterministic outputs |
| Response format | Text | Plain text — no JSON parsing required |

---

## Why Plain Text (Not JSON)?

Earlier versions used JSON output from OpenAI, which required a JSON Parse module in Make.com. This caused repeated mapping errors and module reference failures.

The plain text approach (`SEVERITY: HIGH`, `SUMMARY: ...`, `ACTIONS: ...`) is:
- More reliable in Make.com (no parse module needed)
- Easier to filter in the Router (simple `Contains` text match)
- Resilient to minor formatting variations in OpenAI output
- Simpler to debug

Router filter uses: `2. Result` → Contains → `SEVERITY: CRITICAL`
