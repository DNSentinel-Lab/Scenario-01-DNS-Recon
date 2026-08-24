<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&height=210&section=header&text=Scenario%2001%20%E2%80%94%20DNS%20Reconnaissance&fontSize=38&fontColor=ffffff&animation=fadeIn&fontAlignY=36&desc=Realistic%20Adversary%20%E2%80%A2%20Detection%20Engineering%20%E2%80%A2%20SOC%20%E2%80%A2%20Incident%20Response&descSize=17&descAlignY=58&descColor=00F5FF" width="100%" alt="Scenario 01 DNS Reconnaissance" />

<div align="center">

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=for-the-badge)
![Detection](https://img.shields.io/badge/Detection-v1.0_Validated-0A84FF?style=for-the-badge)
![Splunk](https://img.shields.io/badge/Splunk-Enterprise_10.4.2-000000?style=for-the-badge&logo=splunk&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-Route_53_%2B_Kinesis-FF9900?style=for-the-badge&logo=amazonwebservices&logoColor=white)
![MITRE](https://img.shields.io/badge/MITRE-T1590.002-E34F26?style=for-the-badge)

**A completed DNS reconnaissance exercise executed across a real public Internet boundary. The adversary operated outside the defender AWS account, the SOC Analyst investigated only defender-visible evidence, and Incident Response independently validated the escalation before choosing a proportionate response.**

[Execution Story](SCENARIO-01-EXECUTION.md) · [Detection Engineering](detection-engineering/DETECTION-ENGINEERING.md) · [Adversary](attacker/PROJECT-LEAD-ADVERSARY.md) · [SOC Analyst](soc/SOC-ANALYST-INVESTIGATION.md) · [Incident Response](ir/INCIDENT-RESPONSE.md) · [Runbook](SCENARIO-RUNBOOK.md) · [Final Comparison](exercise/final-comparison.md)

</div>

---

## Scenario at a glance

Scenario 01 tests whether public-authoritative DNS reconnaissance can be detected and investigated without assuming that every unusual DNS pattern is malicious.

**Primary MITRE ATT&CK:** `T1590.002 — Gather Victim Network Information: DNS`  
**Cyber Kill Chain:** Reconnaissance  
**Target namespace:** `soclab.abdul4rehman215.tech`

| Role | Owner | What was completed |
|---|---|---|
| **Project Lead / Adversary** | [Abdul-Rehman](https://github.com/abdul4rehman215) | Preserved exercise separation, generated the authorized Case 01 activity, and executed external DNS reconnaissance from a separate AWS account |
| **SOC Analyst / Threat Hunter** | [Musfira](https://github.com/MUSFIRA-ZAFAR) | Investigated both cases from Splunk evidence, validated AI output, closed Case 01, and escalated Case 02 |
| **Detection Engineer** | [Sonia](https://github.com/sonia11mansha415) | Built the baseline, dashboard, hunting SPL, detection v1.0, scheduled alert and Scenario 01 AI evidence path |
| **Incident Responder / Defender** | [Lubaba](https://github.com/lubaba1513-pixel) | Independently validated Case 02, scoped DNS/Web/VPC evidence, reviewed public exposure and selected the final response |

## Final outcomes

| Case | Detection result | Human conclusion | Final action |
|---|---|---|---|
| **Case 01** | `16 queries / 4 names / 4 types` | **Authorized / Benign True Positive** | Closed by SOC; no IR escalation |
| **Case 02** | `53 queries / 17 names / 6 types` | **True Positive — Suspicious / Likely Unauthorized DNS Reconnaissance** | Escalated to IR; **Preserve + Monitor — No Active Containment** |

> [!IMPORTANT]
> `observed_dns_source` is the resolver/source address seen by Route 53 authoritative logging. It is not automatically the original endpoint that initiated the DNS lookup. That distinction influenced both SOC attribution and the IR containment decision.

---

## How the exercise unfolded

```mermaid
flowchart LR
    DE["Detection Engineering<br/>Sonia"] --> F["Detection v1.0 frozen"]
    F --> C1["Case 01<br/>Authorized DNS validation"]
    F --> C2["Case 02<br/>External DNS reconnaissance"]
    C1 --> R53[Route 53 authoritative telemetry]
    C2 --> R53
    R53 --> SPL[Splunk detection + dashboard]
    SPL --> AI[AI-assisted triage]
    AI --> SOC["SOC Analyst<br/>Musfira"]
    SOC -->|Case 01| CLOSE["Authorized TP<br/>SOC closure"]
    SOC -->|Case 02| IR["Incident Response<br/>Lubaba"]
    IR --> SCOPE["DNS + Nginx + VPC Flow<br/>+ public exposure review"]
    SCOPE --> DEC["Preserve + Monitor<br/>No Active Containment"]
```

The exercise was deliberately **information-separated**. Attacker timing, commands and ground truth were not used to guide the SOC or IR conclusions. Defender decisions were based on Route 53, Splunk, AWS asset context, Nginx, VPC Flow and the scenario AI result.

---

## Detection Engineering foundation

Before the operational exercise began, Sonia built and validated the analyst-facing detection path:

```text
Route 53 telemetry
  → field semantics
  → ingestion timing
  → baseline
  → investigation dashboard
  → hunting SPL
  → detection v1.0
  → positive + benign validation
  → scheduled alert
  → AI evidence contract
```

Final behavioral boundary:

```text
query_count >= 16
unique_names >= 4
distinct_query_types >= 3
```

`NXDOMAIN` remains useful evidence context; it is not a mandatory detection condition.

![Scenario 01 investigation dashboard](../screenshots/detection-engineering/04-dns-investigation-dashboard.png)

*The final Splunk Dashboard Studio view connects high-level DNS behavior with source-aware pivots and raw events.*

Read the full engineering story in [`detection-engineering/DETECTION-ENGINEERING.md`](detection-engineering/DETECTION-ENGINEERING.md).

---

## Case 01 — the alert was real, but the activity was authorized

Case 01 came from the defender-owned `dns-soc-web01` asset. A post-change DNS validation sequence generated a concentrated authoritative-DNS pattern that genuinely crossed Detection v1.0.

![Authorized DNS validation batch](screenshots/attacker/case-01/01-authorized-dns-validation-batch.png)

*The operational validation checked four names across A, AAAA, TXT and CNAME directly against the authoritative path.*

SOC then proved the activity belonged to a known lab asset and obtained authorization context. The correct conclusion was therefore **Authorized / Benign True Positive**, not “false positive.”

![Case 01 detection result](screenshots/soc/case-01/Case-01_E01_Production-Detection-Triggered.png)

*The detection correctly identified reconnaissance-like behavior; business and asset context changed the disposition.*

Deep case record: [`soc/case-01-soc-investigation-closure.md`](soc/case-01-soc-investigation-closure.md)

---

## Case 02 — external DNS reconnaissance

The external adversary operated from a separate AWS account and used public DNS only. The activity progressed from authority/zone inspection into service-name and record-type enumeration.

![Service-name A enumeration](screenshots/attacker/case-02/02-service-name-a-enumeration.png)

*The adversary probed meaningful service/environment labels against the public authoritative namespace rather than relying on attacker-side access to the defender account.*

The defender-side result was a clear reconnaissance outlier:

```text
53 DNS queries
17 unique queried names
6 query types: A, AAAA, MX, NS, SOA, TXT
44 NXDOMAIN / 9 NOERROR
~43-second observed interval
```

![Case 02 activity timeline](screenshots/soc/case-02/Case-02_E03_Activity-Timeline.png)

*Musfira reconstructed the staged DNS bursts from Route 53 telemetry before making the final disposition.*

SOC closed its investigation as **True Positive — Suspicious / Likely Unauthorized DNS Reconnaissance**, High confidence, Medium risk, and transferred an evidence-backed handoff to Incident Response.

Deep case record: [`soc/case-02-soc-investigation-ir-handoff.md`](soc/case-02-soc-investigation-ir-handoff.md)

---

## Incident Response — confirm the behavior, do not over-attribute it

Lubaba did not simply accept the SOC conclusion. IR independently reproduced the core counts, validated the seven-day baseline, expanded the DNS timeline, correlated Nginx and VPC Flow evidence, compared peer DNS sources, and reviewed the Route 53 public hosted-zone records.

![VPC Flow correlation](screenshots/ir/case-02/IR-E05b_VPC-Flow-Client-Correlation.png)

*VPC Flow telemetry corroborated a web connection seen by Nginx, but the web client could not be proven to be the same original endpoint behind the Route 53-observed DNS source.*

The case remained scoped to **Reconnaissance**. No exploitation, persistence, C2 or impact was established.

Final IR decision:

> **PRESERVE + MONITOR ONLY — NO ACTIVE CONTAINMENT**

The decision was intentional: the Route 53 source was resolver-side evidence, no malicious web progression was proven, and the public DNS records were required or benign. Blocking an unproven endpoint would have created attribution and availability risk without an evidence-backed security benefit.

Read [`ir/INCIDENT-RESPONSE.md`](ir/INCIDENT-RESPONSE.md) and [`ir/case-02-validation.md`](ir/case-02-validation.md).

---

## AI was assistance, not authority

The Scenario 01 AI path returned a structured result into `index=dns_soc_ai`, including summary, confidence, observed indicators, MITRE suggestions, missing evidence and response considerations.

Both Musfira and Lubaba treated AI output as a lead to validate, not a verdict to accept.

```text
AI statement
    ↓
What raw evidence proves this?
    ↓
Supported / incomplete / unsupported
    ↓
Human decision
```

That pattern matters most in Case 02: the AI correctly suggested DNS reconnaissance and `T1590.002`, but source identity, authorization and follow-on activity remained human investigation questions.

---

## Network and protocol view

| Layer / view | Scenario 01 evidence |
|---|---|
| **Layer 7 — DNS** | query name, query type, response code, authoritative behavior, NXDOMAIN pattern |
| **Layer 4** | UDP DNS; later TCP 80/443 seen in supporting web/network evidence |
| **Layer 3** | Route 53-observed resolver/source values and VPC Flow source/destination correlation |
| **Application** | Nginx method, URI, HTTP status, user agent |
| **Cloud** | Route 53 hosted-zone records, CloudTrail/SSM asset attribution for Case 01 |
| **SIEM / AI** | Splunk alert, dashboard, raw events, AI enrichment and analyst validation |

---

## Repository map

| Area | Purpose |
|---|---|
| [`SCENARIO-01-EXECUTION.md`](SCENARIO-01-EXECUTION.md) | Concise end-to-end scenario story |
| [`detection-engineering/`](detection-engineering/) | Sonia's engineering lifecycle and validation record |
| [`attacker/`](attacker/) | Project Lead / Adversary story and reproducible adversary playbook |
| [`soc/`](soc/) | Musfira's investigation story, reusable playbook and case dossiers |
| [`ir/`](ir/) | Lubaba's IR investigation, validation matrix and final response decision |
| [`exercise/`](exercise/) | Information-separation protocol and final perspective comparison |
| [`dashboard/`](dashboard/) | Splunk Dashboard Studio JSON and dashboard notes |
| [`spl/`](spl/) | Baseline, hunting, detection, validation SPL and scheduled-alert configuration |
| [`ai/`](ai/) | Scenario-specific AI evidence mapping |
| [`evidence/`](evidence/) | Master evidence map |
| [`screenshots/`](screenshots/) | Curated visual evidence by role and case |

---

## Scenario completion record

| Gate | Status |
|---|---|
| Detection Engineering | ✅ Complete |
| Dashboard / hunting / detection SPL | ✅ Complete |
| Scheduled alert | ✅ Validated |
| AI evidence path | ✅ Validated |
| Realistic adversary execution | ✅ Complete |
| Case 01 SOC investigation | ✅ Closed — Authorized TP |
| Case 02 SOC investigation | ✅ Escalated — Suspicious TP |
| Incident Response validation | ✅ Complete |
| Containment decision | ✅ Preserve + Monitor — No Active Containment |
| Final comparison / lessons | ✅ Complete |

> [!NOTE]
> Scenario 01 demonstrates that a mature security decision is not always “block something.” Detection, attribution, business context and response proportionality all mattered to the final outcome.
