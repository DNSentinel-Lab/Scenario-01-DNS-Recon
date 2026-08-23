# Screenshot Evidence — Scenario 01 DNS Reconnaissance & Enumeration

**Detection Engineering set:** **✅ Curated**

## Detection Engineering evidence

| # | File | What it proves |
|---:|---|---|
| 01 | [`detection-engineering/01-route53-field-mapping.png`](detection-engineering/01-route53-field-mapping.png) | Real Route 53 authoritative fields were mapped before detection logic |
| 02 | [`detection-engineering/02-route53-ingestion-latency.png`](detection-engineering/02-route53-ingestion-latency.png) | Current delivery was separated from older backlog outliers |
| 03 | [`detection-engineering/03-baseline-validation.png`](detection-engineering/03-baseline-validation.png) | Normal source/window ranges used for threshold reasoning |
| 04 | [`detection-engineering/04-dns-investigation-dashboard.png`](detection-engineering/04-dns-investigation-dashboard.png) | Final interactive Splunk Dashboard Studio investigation surface |
| 05 | [`detection-engineering/05-hunting-source-window-behavior.png`](detection-engineering/05-hunting-source-window-behavior.png) | Threshold-free behavioral hunt works |
| 06 | [`detection-engineering/06a-controlled-positive-test-traffic.png`](detection-engineering/06a-controlled-positive-test-traffic.png) | Authorized controlled positive test traffic was generated |
| 07 | [`detection-engineering/06-controlled-positive-detection.png`](detection-engineering/06-controlled-positive-detection.png) | Positive recon-like source/window crossed the detection boundary |
| 08 | [`detection-engineering/07-benign-no-detection.png`](detection-engineering/07-benign-no-detection.png) | Basic benign DNS activity did not trigger the same rule |
| 09 | [`detection-engineering/08-final-detection-v1-validation.png`](detection-engineering/08-final-detection-v1-validation.png) | Final v1.0 evidence row contains the analyst fields |
| 10 | [`detection-engineering/09-scheduled-alert-triggered.png`](detection-engineering/09-scheduled-alert-triggered.png) | Scheduled alert fired automatically |
| 11 | [`detection-engineering/10-raw-event-drilldown.png`](detection-engineering/10-raw-event-drilldown.png) | Analyst can pivot back to exact Route 53 events |
| 12 | [`detection-engineering/11-ai-alert-evidence-contract.png`](detection-engineering/11-ai-alert-evidence-contract.png) | Alert result contains bridge-compatible structured fields |
| 13 | [`detection-engineering/12-ai-triage-indexed.png`](detection-engineering/12-ai-triage-indexed.png) | Structured AI result was indexed back into Splunk |

## Troubleshooting appendix

| File | What it captures |
|---|---|
| [`troubleshooting/t01-kinesis-kvstore-failure.png`](troubleshooting/t01-kinesis-kvstore-failure.png) | Kinesis checkpoint failure tied to KV Store initialization rather than the detection rule |
| [`troubleshooting/t02-compatible-kernel-recovery.png`](troubleshooting/t02-compatible-kernel-recovery.png) | Reversible kernel fallback / service recovery evidence |
| [`troubleshooting/t03-webhook-schema-failure.png`](troubleshooting/t03-webhook-schema-failure.png) | HTTP 400 schema failure that led to the final alert evidence contract |


## Official blind-exercise screenshots — add only after execution

Recommended evidence order:

```text
20-alert-trigger.png
21-soc-dashboard-window.png
22-route53-raw-evidence.png
23-source-history.png
24-ai-triage-human-validation.png
25-soc-disposition.png
26-ir-handoff.png
27-ir-response-verification.png       # only if a response is approved
28-attacker-ground-truth-reveal.png   # publish only after defender record is locked
29-final-comparison.png
```

The attacker-side ground-truth screenshot must **not** be shared with the SOC Analyst during the live investigation.

Keep the final set small: one screenshot should prove one important fact or decision.
