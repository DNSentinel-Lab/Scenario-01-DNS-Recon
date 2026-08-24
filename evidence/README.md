# Evidence — Scenario 01 DNS Reconnaissance & Enumeration

**Status:** ✅ Complete

This folder is the evidence map for the completed Scenario 01 exercise. Visual evidence lives in [`../screenshots/`](../screenshots/); technical case records live beside the role that created them.

## Detection Engineering

- [`../detection-engineering/DETECTION-ENGINEERING.md`](../detection-engineering/DETECTION-ENGINEERING.md)
- [`../detection-engineering/detection-engineering-validation.md`](../detection-engineering/detection-engineering-validation.md)
- [`../spl/`](../spl/)
- [`../screenshots/detection-engineering/`](../screenshots/detection-engineering/)

## Project Lead / Adversary

- [`../attacker/PROJECT-LEAD-ADVERSARY.md`](../attacker/PROJECT-LEAD-ADVERSARY.md)
- [`../attacker/SCENARIO-01-ADVERSARY-PLAYBOOK.md`](../attacker/SCENARIO-01-ADVERSARY-PLAYBOOK.md)
- [`../screenshots/attacker/case-01/`](../screenshots/attacker/case-01/)
- [`../screenshots/attacker/case-02/`](../screenshots/attacker/case-02/)

## SOC Analyst

### Case 01

- [`../soc/case-01-soc-investigation-closure.md`](../soc/case-01-soc-investigation-closure.md)
- [`../screenshots/soc/case-01/`](../screenshots/soc/case-01/)

### Case 02

- [`../soc/case-02-soc-investigation-ir-handoff.md`](../soc/case-02-soc-investigation-ir-handoff.md)
- [`../screenshots/soc/case-02/`](../screenshots/soc/case-02/)

## Incident Response

- [`../ir/INCIDENT-RESPONSE.md`](../ir/INCIDENT-RESPONSE.md)
- [`../ir/case-02-validation.md`](../ir/case-02-validation.md)
- [`../ir/case-02-final-decision.md`](../ir/case-02-final-decision.md)
- [`../screenshots/ir/case-02/`](../screenshots/ir/case-02/)

## Final comparison

- [`../SCENARIO-01-EXECUTION.md`](../SCENARIO-01-EXECUTION.md)
- [`../exercise/final-comparison.md`](../exercise/final-comparison.md)

## Evidence discipline

Keep only evidence that proves a fact, a decision or a reusable lesson.

- Route 53 `observed_dns_source` remains resolver/source evidence, not automatic endpoint attribution.
- AI output is supporting analyst context, not primary evidence.
- SOC and IR conclusions should be reproducible from defender telemetry.
- Screenshots should have readable names, visible time ranges where relevant, and captions explaining what they prove.
