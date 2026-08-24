<a id="top"></a>

> 🧭 [Scenario 01](../README.md) › [Soc](README.md) › **SOC Analyst Investigation — Scenario 01 DNS Reconnaissance**

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=flat-square)
![DNSentinel](https://img.shields.io/badge/DNSentinel-Technical_Record-00F5FF?style=flat-square)

---

# SOC Analyst Investigation — Scenario 01 DNS Reconnaissance

**SOC Analyst / Threat Hunter:** [Musfira](https://github.com/MUSFIRA-ZAFAR)  
**Detection:** `Scenario 01 - Possible DNS Reconnaissance`  
**MITRE ATT&CK:** `T1590.002 — Gather Victim Network Information: DNS`  
**Cyber Kill Chain:** Reconnaissance  
**Status:** ✅ Two cases investigated and dispositioned

This document tells Musfira's Scenario 01 investigation story. It summarizes how she worked from alerts to raw evidence, used AI as assistance, applied business context and reached two different outcomes from the same detection.

The detailed evidence/SPL for each case remains in the case dossiers:

- [`case-01-soc-investigation-closure.md`](case-01-soc-investigation-closure.md)
- [`case-02-soc-investigation-ir-handoff.md`](case-02-soc-investigation-ir-handoff.md)

## 1. Analyst operating rule

```text
Alert = lead
AI = assistance
Raw logs = evidence
Business context = meaning
Human analyst = decision
```

Musfira was not given attacker commands, source ground truth or the intended case result. She worked from defender-visible evidence and allowed the evidence to support one of four outcomes: suspicious TP, authorized TP, false positive or inconclusive.

## 2. Investigation method

The workflow used across both cases was consistent:

```text
Alert triage
→ raw Route 53 events
→ exact timeline
→ source behavior
→ query-name breadth
→ record-type diversity
→ response pattern
→ historical baseline
→ asset / business context
→ AI review
→ AI-vs-human validation
→ 5W1H
→ risk
→ disposition
→ closure or IR handoff
```

That consistency is what made the different final outcomes defensible.

---

## Case 01 — Authorized / Benign True Positive

### 3. Detection and raw behavior

The production rule returned:

```text
Observed source: 100.49.192.164
16 queries
4 unique names
4 query types
A, AAAA, CNAME, TXT
```

![Case 01 detection result](../screenshots/soc/case-01/Case-01_E01_Production-Detection-Triggered.png)

Raw events confirmed a concentrated UDP DNS burst against the base namespace and service-style labels.

![Case 01 raw DNS events](../screenshots/soc/case-01/Case-01_E02_Raw-DNS-Events.png)

### 4. The key SOC question: who owns the source?

The DNS behavior alone looked like reconnaissance. Musfira therefore moved into attribution and business context rather than jumping directly to containment.

CloudTrail/SSM and resolver/private-IP evidence linked the source to the known defender asset `dns-soc-web01` (`10.50.10.10`).

![CloudTrail / SSM asset attribution](../screenshots/soc/case-01/Case-01_E06_CloudTrail-SSM-Asset-Attribution.png)

![AWS EC2 asset context](../screenshots/soc/case-01/Case-01_E08_AWS-EC2-Asset-Context.png)

Authorization was then confirmed as approved lab activity.

### 5. Case 01 5W1H

| Question | Evidence-backed answer |
|---|---|
| **Who** | Source `100.49.192.164`, attributed to `dns-soc-web01` / private IP `10.50.10.10` |
| **What** | 16 DNS queries across 4 names and A/AAAA/CNAME/TXT |
| **When** | `2026-08-23 08:41:55`; concentrated in one second |
| **Where** | Public `soclab.abdul4rehman215.tech` authoritative namespace |
| **Why** | Approved post-change / controlled-lab DNS validation |
| **How** | Concentrated UDP DNS enumeration-like requests |

### 6. AI vs human — Case 01

AI correctly suggested possible DNS reconnaissance and `T1590.002 / Reconnaissance`, but it also identified missing timing, ownership, authorization and network context.

Musfira resolved those gaps from human evidence:

- exact one-second activity window;
- UDP protocol;
- seven-day history;
- cloud asset attribution;
- authorization confirmation.

The AI summary helped structure questions; it did not decide the case.

### 7. Case 01 decision

> **AUTHORIZED / BENIGN TRUE POSITIVE — High confidence**

The detection behaved correctly. The business purpose made the behavior authorized.

**Action:** SOC closure. No IR escalation.

Detection Engineering feedback: do not weaken the production threshold because of one authorized event; consider a narrowly scoped asset/change-aware suppression only if this approved activity becomes routine.

---

## Case 02 — Suspicious / Likely Unauthorized DNS Reconnaissance

### 8. Detection result

Case 02 was materially different:

```text
Route 53-observed DNS source: 54.242.155.119
53 queries
17 unique names
6 query types
44 NXDOMAIN / 9 NOERROR
10:21:51 → 10:22:34
```

![Case 02 production detection](../screenshots/soc/case-02/Case-02_E01_Production-Detection-Triggered.png)

> [!IMPORTANT]
> Musfira treated `54.242.155.119` as an **observed DNS source / resolver**, not a confirmed attacker endpoint.

### 9. Reconstruct the attack behavior

The 10-second timeline showed distinct stages:

- one-name / six-type interrogation;
- broad A-name enumeration;
- broad TXT-name enumeration;
- another broad A burst.

![Case 02 activity timeline](../screenshots/soc/case-02/Case-02_E03_Activity-Timeline.png)

The query-name view showed systematic service/environment guessing.

![Case 02 query-name breadth](../screenshots/soc/case-02/Case-02_E04_Query-Name-Breadth.png)

The response distribution added context:

```text
44 NXDOMAIN = 83.02%
9 NOERROR   = 16.98%
```

![Case 02 response pattern](../screenshots/soc/case-02/Case-02_E05_Response-Code-Pattern.png)

Musfira did not treat NXDOMAIN as proof by itself; the conclusion came from the combined volume, name breadth, record diversity, timing and context.

### 10. Historical and ownership context

The source had no comparable prior activity in the available seven-day baseline.

![Case 02 seven-day baseline](../screenshots/soc/case-02/Case-02_E06_Historical-Baseline.png)

Cross-telemetry checks found the source only in `aws:kinesis` within the available `dns_soc_aws` evidence. No defender-owned asset, approved scanner or operational purpose was established.

![Case 02 source ownership unresolved](../screenshots/soc/case-02/Case-02_E09_Source-Ownership-Unresolved.png)

### 11. Case 02 5W1H

| Question | Evidence-backed answer |
|---|---|
| **Who** | Route 53-observed DNS source `54.242.155.119`; original endpoint/ownership unresolved |
| **What** | 53-query structured DNS reconnaissance across 17 names and 6 record types |
| **When** | `2026-08-23 10:21:51–10:22:34` |
| **Where** | Public `soclab.abdul4rehman215.tech` authoritative namespace |
| **Why** | Intent cannot be proven from DNS alone; first-seen systematic enumeration with no known authorization supported a suspicious assessment |
| **How** | UDP DNS in staged bursts combining base-domain record interrogation and service-name enumeration |

### 12. AI vs human — Case 02

The case-correlated AI result suggested possible DNS reconnaissance, `T1590.002` and Reconnaissance with medium confidence.

![Case 02 AI mapping and confidence](../screenshots/soc/case-02/Case-02_E08c_AI-Mapping-and-Confidence.png)

AI correctly left important questions open: source identity, authorization, detailed timing and follow-on activity.

![Case 02 AI missing evidence](../screenshots/soc/case-02/Case-02_E08d_AI-Missing-Evidence-and-Network-Context.png)

Musfira resolved timing, protocol, name breadth, response pattern and baseline from raw telemetry. Ownership and authorization remained unresolved, so the case required IR rather than automatic blocking.

### 13. Case 02 risk and disposition

**SOC risk:** Medium  
**SOC confidence:** High

Reasoning:

- first-seen source behavior in available baseline;
- systematic service-name probing;
- six record types;
- short staged bursts;
- high query-name breadth;
- no known authorization or ownership correlation;
- no evidence yet of exploitation, persistence, C2 or impact.

> **TRUE POSITIVE — Suspicious / Likely Unauthorized DNS Reconnaissance**

**Action:** evidence-backed handoff to Incident Response.

### 14. What Musfira handed to IR

The handoff preserved:

- alert/detection metrics;
- raw Route 53 events;
- exact timeline;
- query-name breadth;
- response distribution;
- seven-day history;
- source-ownership gap;
- AI-vs-human assessment;
- 5W1H;
- risk and confidence;
- unresolved questions requiring IR.

IR was specifically asked to validate the SOC facts, investigate any web/network follow-up, review public DNS exposure, assess attribution and decide whether containment was justified.

### 15. SOC lessons

- A true detection is not automatically a malicious incident.
- Source identity and authorization are separate investigation questions.
- A first-seen external-looking resolver should still be described neutrally until attribution improves.
- NXDOMAIN becomes meaningful when combined with behavior, not when used alone.
- AI is most useful when it exposes missing evidence rather than when it sounds confident.
- The same detection can support two correct but very different dispositions.

### 16. Evidence and next stage

Full Case 01 evidence: [`case-01-soc-investigation-closure.md`](case-01-soc-investigation-closure.md)  
Full Case 02 handoff: [`case-02-soc-investigation-ir-handoff.md`](case-02-soc-investigation-ir-handoff.md)  
IR continuation: [`../ir/INCIDENT-RESPONSE.md`](../ir/INCIDENT-RESPONSE.md)

---

<div align="center">

[🏠 Scenario Home](../README.md) · [📁 Soc](README.md) · [⬆ Back to top](#top)

<sub>DNSentinel Lab · Evidence-first DNS security engineering</sub>

</div>
