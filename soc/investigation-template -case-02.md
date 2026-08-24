<a id="top"></a>

> 🧭 [Scenario 01](../README.md) › [Soc](README.md) › **Scenario 01 SOC Investigation Template**

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=flat-square)
![DNSentinel](https://img.shields.io/badge/DNSentinel-Technical_Record-00F5FF?style=flat-square)

---

# Scenario 01 SOC Investigation Template

## Alert identity

| Field | Value |
|---|---|
| Analyst | Musfira |
| Alert ID | Case-02 — Production Detection v1.0 |
| Alert time UTC | 2026-08-23 10:21:51 |
| Severity | Medium (risk); High (SOC disposition confidence) |
| MITRE | `T1590.002` |
| Cyber Kill Chain | Reconnaissance |

## 5W1H

| Question | Notes |
|---|---|
| **Who?** | Observed DNS source 54.242.155.119. Available defender telemetry (CloudTrail/S3 within `dns_soc_aws`) did not attribute it to a known owned asset, approved scanner, or authorized operational source. |
| **What?** | Structured DNS reconnaissance: 53 queries, 17 unique names, six record types (A, AAAA, MX, NS, SOA, TXT), repeated service-name probing, and a high NXDOMAIN proportion. |
| **When?** | 2026-08-23 10:21:51 to 10:22:34 (approximately 43 seconds), delivered in staged bursts. |
| **Where?** | Public authoritative namespace `soclab.abdul4rehman215.tech`, observed in `index=dns_soc_aws sourcetype=aws:kinesis`. |
| **Why suspicious?** | Systematic first-seen enumeration pattern (no comparable prior 7-day behavior for this source), high query-name breadth, six record types, staged bursts, 83.02% NXDOMAIN rate, and no known authorization/ownership correlation in available defender telemetry. DNS evidence alone cannot prove intent, but the totality of evidence supports a suspicious/likely unauthorized assessment. |
| **How?** | UDP DNS issued in staged bursts: multi-record interrogation of the base domain, a broad A-record burst, a broad TXT-record burst, then another broad A-record burst across service-oriented names (admin, api, backup, db, dev, internal, mail, monitor, portal, prod, stage, staging, test, vpn). |

## Detection evidence

| Metric | Value |
|---|---|
| Observed DNS source | 54.242.155.119 |
| Query count | 53 |
| Unique names | 17 |
| Distinct query types | 6 |
| Query types | A, AAAA, MX, NS, SOA, TXT |
| Response codes | NXDOMAIN (44), NOERROR (9) |
| NXDOMAIN count | 44 (83.02%) |

## Supporting evidence

- Route 53 raw-event search: [Case-02_E02_Raw-DNS-Events.png](../screenshots/soc/case-02/Case-02_E02_Raw-DNS-Events.png)
- Dashboard screenshot/link: [Case-02_E01_Production-Detection-Triggered.png](../screenshots/soc/case-02/Case-02_E01_Production-Detection-Triggered.png) · [Case-02_E03_Activity-Timeline.png](../screenshots/soc/case-02/Case-02_E03_Activity-Timeline.png) · [Case-02_E04_Query-Name-Breadth.png](../screenshots/soc/case-02/Case-02_E04_Query-Name-Breadth.png) · [Case-02_E05_Response-Code-Pattern.png](../screenshots/soc/case-02/Case-02_E05_Response-Code-Pattern.png)
- Historical-source search: [Case-02_E06_Historical-Baseline.png](../screenshots/soc/case-02/Case-02_E06_Historical-Baseline.png) (Last 7 days; confirmed this was the only observed behavior for the source in the available history)
- Nginx follow-up: Not applicable — no web-tier telemetry was pulled for this case
- VPC Flow follow-up: Not applicable — cross-telemetry check found the source only in `aws:kinesis`, with no CloudTrail/S3 ownership correlation
- Other context:
  - [Case-02_E07_Cross-Telemetry-Correlation.png](../screenshots/soc/case-02/Case-02_E07_Cross-Telemetry-Correlation.png) — cross-telemetry correlation
  - [Case-02_E08a_AI-Alert-Fields.png](../screenshots/soc/case-02/Case-02_E08a_AI-Alert-Fields.png) · [Case-02_E08b_AI-Query-Samples.png](../screenshots/soc/case-02/Case-02_E08b_AI-Query-Samples.png) · [Case-02_E08c_AI-Mapping-and-Confidence.png](../screenshots/soc/case-02/Case-02_E08c_AI-Mapping-and-Confidence.png) · [Case-02_E08d_AI-Missing-Evidence-and-Network-Context.png](../screenshots/soc/case-02/Case-02_E08d_AI-Missing-Evidence-and-Network-Context.png) · [Case-02_E08e_AI-Summary-and-Response-Considerations.png](../screenshots/soc/case-02/Case-02_E08e_AI-Summary-and-Response-Considerations.png) — AI-generated summary and human validation
  - [Case-02_E09_Source-Ownership-Unresolved.png](../screenshots/soc/case-02/Case-02_E09_Source-Ownership-Unresolved.png) — source ownership/authorization explicitly unresolved
  - Case-02_E10_Final-Disposition-and-IR-Escalation.txt — SOC final disposition and IR escalation record (text evidence file, not a screenshot; link once its repo location is confirmed)

## AI validation

| Check | Result |
|---|---|
| Facts accurate | Yes — AI event (query_count=53, event time 10:21:51) matches raw case evidence |
| Reasoning supported | Yes |
| MITRE/kill-chain useful | Yes — correctly mapped `T1590.002` / Reconnaissance with medium confidence |
| Unsupported assumptions found | None — AI explicitly stated that source identity, authorization, timing/burst context, and follow-on activity were not established by the supplied alert payload; it did not assert automatic containment |
| Overall AI assessment | Correct |

## SOC disposition

Select one:

- [x] TP — suspicious/unauthorized reconnaissance
- [ ] TP — real behavior but verified authorized/benign context
- [ ] FP — normal/non-recon behavior incorrectly matched the detection
- [ ] Inconclusive — more context required

**Confidence:** High

**Evidence-backed conclusion:** The production detection identified 54.242.155.119 performing structured DNS reconnaissance against `soclab.abdul4rehman215.tech`. Human analysis confirmed 53 queries across 17 unique names and six DNS record types, delivered in staged bursts over ~43 seconds, with 83.02% NXDOMAIN responses. The source was first-seen in the available 7-day baseline and appeared only in `aws:kinesis` within `dns_soc_aws` — no defender-side ownership or approved-purpose correlation was established despite cross-telemetry checks. The evidence supports a high-confidence suspicious/likely unauthorized reconnaissance disposition. No evidence currently establishes exploitation, persistence, command-and-control, exfiltration, or impact, so the case remains scoped to Reconnaissance pending IR follow-up.

## IR handoff

- Escalated: Yes
- Time escalated UTC: 2026-08-23 (investigation closeout, same-day escalation)
- Reason: Systematic first-seen DNS enumeration against a public namespace, high query-name breadth, six record types, staged bursts, high NXDOMAIN rate, and no known authorization/ownership correlation in the available defender telemetry.
- What remains unknown: Whether the discovered names were subsequently accessed; any follow-on web/network activity; source ownership/authorization (explicitly unresolved per E09).
- Evidence references: E01–E09 (screenshots), E10 (Case-02_E10_Final-Disposition-and-IR-Escalation.txt — disposition/escalation record)

## Post-ground-truth comparison

Complete only after the Project Lead reveals attacker ground truth.

- Did SOC identify the correct time window?
- Did SOC identify the observable source correctly?
- Did SOC reconstruct the queried names/types?
- Did SOC avoid unsupported attribution?
- Did AI agree with the raw evidence?
- Was the final disposition correct?
- Detection feedback for Sonia: Detection performed as designed on genuinely reconnaissance-like behavior; no threshold change indicated by this case. IR should validate follow-on web/network activity, determine whether discovered names were subsequently accessed, decide on proportionate containment, and preserve additional evidence.

---

<div align="center">

[🏠 Scenario Home](../README.md) · [📁 Soc](README.md) · [⬆ Back to top](#top)

<sub>DNSentinel Lab · Evidence-first DNS security engineering</sub>

</div>
