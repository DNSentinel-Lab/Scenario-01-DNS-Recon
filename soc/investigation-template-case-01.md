# Scenario 01 SOC Investigation Template

## Alert identity

| Field | Value |
|---|---|
| Analyst | Musfira |
| Alert ID | Case-01 — Production Detection v1.0 |
| Alert time UTC | 2026-08-23 08:41:55 |
| Severity | Medium (detection significance); Low (incident risk, post-authorization) |
| MITRE | `T1590.002` |
| Cyber Kill Chain | Reconnaissance |

## 5W1H

| Question | Notes |
|---|---|
| **Who?** | Observed source 100.49.192.164, attributed through CloudTrail/SSM and resolver evidence to EC2 instance `dns-soc-web01` (i-077d7b9a9de0b1387), private IP 10.50.10.10, in SOC-LAB-VPC / SOC-TARGET-SUBNET. Lab owner confirmed the activity as authorized. |
| **What?** | 16 DNS queries across 4 unique names and 4 record types (A, AAAA, CNAME, TXT), with NOERROR and NXDOMAIN responses — a concentrated enumeration-like burst. |
| **When?** | Alert time 2026-08-23 08:41:55; all 16 queries occurred within the same one-second bucket. |
| **Where?** | Public authoritative namespace `soclab.abdul4rehman215.tech`; asset resides in SOC-LAB-VPC / SOC-TARGET-SUBNET. |
| **Why suspicious?** | Pattern matched the frozen production reconnaissance thresholds (≥16 queries, ≥4 unique names, ≥4 record types). DNS logs alone could not establish intent; asset attribution plus manual owner confirmation resolved it as an approved controlled-lab test rather than malicious activity. |
| **How?** | UDP DNS requests issued as a concentrated burst against the base domain and service-oriented labels (api, mail, www, etc.), all within a one-second window. |

## Detection evidence

| Metric | Value |
|---|---|
| Observed DNS source | 100.49.192.164 |
| Query count | 16 |
| Unique names | 4 |
| Distinct query types | 4 |
| Query types | A, AAAA, CNAME, TXT |
| Response codes | NOERROR, NXDOMAIN |
| NXDOMAIN count | Present but not the majority driver of the disposition (exact count not separately isolated in this case; base domain returned NOERROR) |

## Supporting evidence

- Route 53 raw-event search: [Case-01_E02_Raw-DNS-Events.png](../screenshots/soc/case-01/Case-01_E02_Raw-DNS-Events.png)
- Dashboard screenshot/link: [Case-01_E01_Production-Detection-Triggered.png](../screenshots/soc/case-01/Case-01_E01_Production-Detection-Triggered.png) · [Case-01_E03_Activity-Timeline.png](../screenshots/soc/case-01/Case-01_E03_Activity-Timeline.png)
- Historical-source search: [Case-01_E04_Historical-Baseline.png](../screenshots/soc/case-01/Case-01_E04_Historical-Baseline.png) (Last 7 days)
- Nginx follow-up: Not applicable — no web-tier telemetry was pulled for this case
- VPC Flow follow-up: Not applicable — attribution was completed via CloudTrail/SSM and resolver/private-IP correlation instead
- Other context:
  - [Case-01_E05_Cross-Telemetry-Correlation.png](../screenshots/soc/case-01/Case-01_E05_Cross-Telemetry-Correlation.png) — cross-telemetry presence
  - [Case-01_E06_CloudTrail-SSM-Asset-Attribution.png](../screenshots/soc/case-01/Case-01_E06_CloudTrail-SSM-Asset-Attribution.png) — CloudTrail/SSM asset attribution
  - [Case-01_E07_Resolver-Private-IP-Correlation.png](../screenshots/soc/case-01/Case-01_E07_Resolver-Private-IP-Correlation.png) — resolver/private-IP correlation to 10.50.10.10
  - [Case-01_E08_AWS-EC2-Asset-Context.png](../screenshots/soc/case-01/Case-01_E08_AWS-EC2-Asset-Context.png) — AWS EC2 console context for `dns-soc-web01`
  - [Case-01_E09a_AI-Alert-Fields.png](../screenshots/soc/case-01/Case-01_E09a_AI-Alert-Fields.png) · [Case-01_E09b_AI-Evidence-Fields.png](../screenshots/soc/case-01/Case-01_E09b_AI-Evidence-Fields.png) · [Case-01_E09c_AI-Mapping-and-Confidence.png](../screenshots/soc/case-01/Case-01_E09c_AI-Mapping-and-Confidence.png) · [Case-01_E09d_AI-Missing-Evidence-and-Network-Context.png](../screenshots/soc/case-01/Case-01_E09d_AI-Missing-Evidence-and-Network-Context.png) · [Case-01_E09e_AI-Summary-and-Response-Considerations.png](../screenshots/soc/case-01/Case-01_E09e_AI-Summary-and-Response-Considerations.png) — AI enrichment and human validation
  - Case-01_E10_Authorization-Confirmation.txt — manual lab-owner authorization confirmation (text evidence file, not a screenshot; link once its repo location is confirmed)

## AI validation

| Check | Result |
|---|---|
| Facts accurate | Yes |
| Reasoning supported | Yes |
| MITRE/kill-chain useful | Yes — correctly mapped `T1590.002` / Reconnaissance |
| Unsupported assumptions found | None — AI did not label the source an "attacker IP" and did not push for automatic containment |
| Overall AI assessment | Correct |

## SOC disposition

Select one:

- [ ] TP — suspicious/unauthorized reconnaissance
- [x] TP — real behavior but verified authorized/benign context
- [ ] FP — normal/non-recon behavior incorrectly matched the detection
- [ ] Inconclusive — more context required

**Confidence:** High

**Evidence-backed conclusion:** The frozen production detection correctly identified reconnaissance-like DNS behavior from 100.49.192.164. Human investigation confirmed all 16 queries fired in a single one-second burst over UDP, checked the 7-day baseline (no comparable prior behavior), and used CloudTrail/SSM plus resolver/private-IP correlation to attribute the public source to known SOC lab asset `dns-soc-web01` (i-077d7b9a9de0b1387, private IP 10.50.10.10). The lab owner confirmed the activity was an approved, controlled test. The detection performed exactly as designed; the underlying activity was authorized rather than malicious, so this closes as an Authorized / Benign True Positive.

## IR handoff

- Escalated: No
- Time escalated UTC: Not applicable — closed by SOC
- Reason: Authorization was confirmed by the lab/environment owner; no malicious compromise was established, so no IR escalation was required.
- What remains unknown: No separate change-ticket or authorization screenshot was supplied during this exercise; add one if audit-grade external proof is required for publication.
- Evidence references: E01–E09 (screenshots), E10 (Case-01_E10_Authorization-Confirmation.txt — authorization/closure record)

## Post-ground-truth comparison

Complete only after the Project Lead reveals attacker ground truth.

- Did SOC identify the correct time window?
- Did SOC identify the observable source correctly?
- Did SOC reconstruct the queried names/types?
- Did SOC avoid unsupported attribution?
- Did AI agree with the raw evidence?
- Was the final disposition correct?
- Detection feedback for Sonia: The production rule behaved as designed and should not be weakened because of this authorized event. If the same approved activity will recur operationally, consider a narrowly scoped, documented asset/change-aware suppression only after review.
