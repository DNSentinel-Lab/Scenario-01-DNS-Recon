# SOC Analyst Workspace — Scenario 01

**SOC Analyst / Threat Hunter:** [Musfira](https://github.com/MUSFIRA-ZAFAR)  
**Status:** ✅ Case 01 and Case 02 completed

Musfira investigated Scenario 01 from defender-visible evidence and reached two different evidence-backed dispositions from the same production detection.

## Start here

- [`SOC-ANALYST-INVESTIGATION.md`](SOC-ANALYST-INVESTIGATION.md) — flagship investigation story covering both cases.
- [`case-01-soc-investigation-closure.md`](case-01-soc-investigation-closure.md) — full Case 01 evidence, 5W1H and SOC closure.
- [`case-02-soc-investigation-ir-handoff.md`](case-02-soc-investigation-ir-handoff.md) — full Case 02 evidence, 5W1H, risk and IR handoff.
- [`SOC-ANALYST-PLAYBOOK.md`](SOC-ANALYST-PLAYBOOK.md) — reusable step-by-step investigation workflow.
- [`investigation-template.md`](investigation-template.md) — concise reusable investigation record.

## Final SOC outcomes

| Case | Disposition | Confidence | Escalation |
|---|---|---|---|
| Case 01 | **Authorized / Benign True Positive** | High | Closed by SOC |
| Case 02 | **True Positive — Suspicious / Likely Unauthorized DNS Reconnaissance** | High | Escalated to IR |

## Analyst principle

```text
Alert = lead
AI = assistance
Raw logs = evidence
Business context = meaning
Human analyst = decision
```
