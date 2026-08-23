# Evidence — Scenario 01 DNS Reconnaissance & Enumeration

**Detection Engineering evidence:** **✅ Complete**  
**Official blind exercise evidence:** ⏳ Pending execution

This folder preserves structured evidence and decision records. Screenshots are indexed separately in [`../screenshots/README.md`](../screenshots/README.md).

## Existing engineering evidence

[`detection-engineering-validation.md`](detection-engineering-validation.md) records the completed gates:

- Route 53 field mapping;
- source semantics;
- ingestion timing;
- baseline;
- controlled positive validation;
- benign validation;
- detection v1.0;
- scheduled alert;
- raw-event drilldown;
- AI evidence contract;
- end-to-end AI return to Splunk.

## Official blind-exercise evidence model

The live exercise should produce separate evidence from each role:

### Adversary — private until reveal

Use [`../attacker/ground-truth-template.md`](../attacker/ground-truth-template.md).

Keep private during the investigation:

- source public IP(s);
- UTC start/end;
- commands;
- queried names/types;
- AXFR result;
- optional web follow-up.

### SOC Analyst

Use [`../soc/investigation-template.md`](../soc/investigation-template.md).

Preserve:

- alert ID/results;
- dashboard view;
- raw Route 53 evidence;
- historical comparison;
- supporting web/network context;
- 5W1H notes;
- AI validation;
- TP / authorized TP / FP / inconclusive disposition;
- confidence and handoff time.

### IR / Defender

Preserve:

- received SOC finding;
- response decision;
- evidence preserved;
- containment/change if approved;
- before/after verification;
- final response status.

### Final comparison

After ground-truth reveal, complete:

[`../exercise/final-comparison-template.md`](../exercise/final-comparison-template.md)

This compares:

```text
attacker ground truth
vs telemetry visibility
vs Detection v1.0
vs AI output
vs SOC conclusion
vs IR response
```

## Evidence rule

Keep only evidence that proves a fact, a decision or a reusable lesson.

Do not:

- publish actual attacker ground truth before the SOC decision;
- call `observed_dns_source` a confirmed attacker without evidence;
- treat AI text as primary evidence;
- publish repetitive screenshots that do not change the investigation story.
