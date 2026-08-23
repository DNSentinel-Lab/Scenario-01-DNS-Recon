# SOC Analyst Workspace — Scenario 01

**Role owner:** Musfira — SOC Analyst / Threat Hunter  
**Official exercise model:** blind investigation  
**Primary evidence:** Route 53 authoritative DNS telemetry in Splunk

The SOC Analyst does not receive attacker start time, source IP, commands, queried names or attacker ground truth before the investigation is complete.

## Files

- [`SOC-ANALYST-PLAYBOOK.md`](SOC-ANALYST-PLAYBOOK.md) — live, click-by-click investigation workflow.
- [`investigation-template.md`](investigation-template.md) — concise 5W1H notes, evidence and IR handoff template.

## Analyst rule

```text
Alert = lead
AI = assistance
Raw logs = evidence
Human analyst = decision
```

The analyst should be willing to conclude **true suspicious activity**, **true but authorized/benign activity**, **false positive**, or **inconclusive**. Do not force a verdict when the evidence is incomplete.
