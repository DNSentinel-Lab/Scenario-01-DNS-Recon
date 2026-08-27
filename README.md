<a id="top"></a>
<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=0,2,12,20,24&height=225&section=header&text=Scenario%2001%20%E2%80%94%20DNS%20Reconnaissance&fontSize=38&fontColor=ffffff&animation=fadeIn&fontAlignY=36&desc=DNSentinel%20Lab%20%7C%20Completed%20SOC%20Case%20File&descSize=16&descAlignY=58&descColor=00F5FF" width="100%" alt="Scenario 01 — DNS Reconnaissance" />

<div align="center">

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=700&size=21&duration=2900&pause=900&color=00F5FF&center=true&vCenter=true&repeat=true&width=960&height=72&lines=Public+DNS+%E2%86%92+Detection+%E2%86%92+Investigation+%E2%86%92+IR;Two+Cases+%7C+One+Detection+%7C+Evidence-Driven+Decisions" alt="Scenario workflow animation" />

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=for-the-badge)
![AWS](https://img.shields.io/badge/AWS-Security_Lab-FF9900?style=for-the-badge&logo=amazonwebservices&logoColor=white)
![Splunk](https://img.shields.io/badge/Splunk-Enterprise-000000?style=for-the-badge&logo=splunk&logoColor=white)
![DNS](https://img.shields.io/badge/DNS-Security-00B8D9?style=for-the-badge)
![MITRE](https://img.shields.io/badge/MITRE-T1590.002-E34F26?style=for-the-badge)
![Route53](https://img.shields.io/badge/Route_53-Authoritative_DNS-8C4FFF?style=for-the-badge&logo=amazonroute53&logoColor=white)
![AI](https://img.shields.io/badge/AI-Assisted-7B2CBF?style=for-the-badge)

<br/>

![Stars](https://img.shields.io/github/stars/DNSentinel-Lab/Scenario-01-DNS-Recon?style=flat-square)
![Forks](https://img.shields.io/github/forks/DNSentinel-Lab/Scenario-01-DNS-Recon?style=flat-square)
![Last Commit](https://img.shields.io/github/last-commit/DNSentinel-Lab/Scenario-01-DNS-Recon?style=flat-square)
![Repo Size](https://img.shields.io/github/repo-size/DNSentinel-Lab/Scenario-01-DNS-Recon?style=flat-square)
![License](https://img.shields.io/github/license/DNSentinel-Lab/Scenario-01-DNS-Recon?style=flat-square)
![Issues](https://img.shields.io/github/issues/DNSentinel-Lab/Scenario-01-DNS-Recon?style=flat-square)

**A completed, evidence-backed SOC case study covering realistic external DNS reconnaissance, Detection Engineering, AI-assisted investigation, human SOC analysis and independent Incident Response.**

[🏗️ Infrastructure](https://github.com/DNSentinel-Lab/DNS-Lab-Infrastructure) · [**🔎 Scenario 01**](https://github.com/DNSentinel-Lab/Scenario-01-DNS-Recon) · [🧬 Scenario 02](https://github.com/DNSentinel-Lab/Scenario-02-DGA) · [🔄 Scenario 03](https://github.com/DNSentinel-Lab/Scenario-03-Fast-Flux) · [🛰️ Scenario 04](https://github.com/DNSentinel-Lab/Scenario-04-DNS-Tunneling)

</div>

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

## 🎯 Mission Brief

| Field | Scenario record |
|---|---|
| **Mission** | Detect and investigate public-authoritative DNS reconnaissance without assuming that every unusual DNS pattern is malicious |
| **Status** | ✅ Completed end-to-end SOC exercise |
| **MITRE ATT&CK** | `T1590.002 — Gather Victim Network Information: DNS` |
| **Cyber Kill Chain** | Reconnaissance |
| **Target namespace** | `soclab.abdul4rehman215.tech` |
| **Telemetry** | Route 53 authoritative DNS, Splunk, AWS context, Nginx, VPC Flow, AI result |
| **Operational result** | Case 01 closed by SOC; Case 02 escalated and independently validated by IR |

### What this scenario proves

A correct alert is not the same thing as a confirmed malicious incident. This scenario demonstrates the full decision chain from behavior-based DNS detection through source attribution, AI validation, SOC disposition, independent IR review and proportionate response.

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

## 🏗️ Scenario Architecture

```mermaid
flowchart TB

    %% =====================================================
    %% TOP ROW — OBSERVE → DETECT → INVESTIGATE
    %% =====================================================
    subgraph TOP[" "]
        direction LR


        %% -------------------------------------------------
        %% 1 — EXTERNAL ACTIVITY
        %% -------------------------------------------------
        subgraph EXT[" "]
            direction TB

            EH["🌐 1 · EXTERNAL ACTIVITY"]

            ADV["🎯 External Adversary<br/>Separate AWS Account"]

            R53["🌍 Route 53<br/>Authoritative DNS"]

            EH ~~~ ADV
            ADV --> R53
        end


        %% -------------------------------------------------
        %% 2 — TELEMETRY + DETECTION
        %% -------------------------------------------------
        subgraph DETECT[" "]
            direction TB

            TH["📡 2 · TELEMETRY + DETECTION"]

            SPL["🟢 Splunk<br/>Enterprise"]

            DET["🛡️ Detection<br/>v1.0"]

            TH ~~~ SPL
            SPL --> DET
        end


        %% -------------------------------------------------
        %% 3 — SOC ANALYSIS
        %% -------------------------------------------------
        subgraph ANALYZE[" "]
            direction TB

            AH["🔍 3 · SOC ANALYSIS"]

            AI["🤖 AI-Assisted<br/>Summary"]

            SOC["👩‍💻 SOC Investigation<br/>Evidence Validation"]

            AH ~~~ AI
            AI --> SOC
        end
    end


    %% =====================================================
    %% MAIN TOP-ROW FLOW
    %% No long grey edge labels
    %% =====================================================
    R53 --> SPL
    DET --> AI
    DET --> SOC


    %% =====================================================
    %% 4 — DECISION + OUTCOME
    %% =====================================================
    subgraph OUTCOME[" "]
        direction TB

        OH["⚖️ 4 · DECISION + OUTCOME"]

        DEC{"⚖️ Analyst Decision<br/>Evidence Backed"}

        OH ~~~ DEC


        %% -------------------------------------------------
        %% TWO OUTCOMES
        %% -------------------------------------------------
        subgraph CASES[" "]
            direction LR


            %% CASE 01
            subgraph CASE1[" "]
                direction TB

                AUTH["✅ CASE 01<br/>Authorized True Positive"]

                CLOSE["📁 SOC Closure<br/>Document + Preserve"]

                AUTH --> CLOSE
            end


            %% CASE 02
            subgraph CASE2[" "]
                direction TB

                IR["🚨 CASE 02<br/>Incident Response"]

                CORR["🔗 Evidence Correlation<br/>DNS · Nginx · VPC Flow"]

                FINAL["📋 Preserve + Monitor<br/>No Active Containment"]

                IR --> CORR --> FINAL
            end
        end

        DEC --> AUTH
        DEC --> IR
    end


    %% =====================================================
    %% SOC → HUMAN DECISION
    %% =====================================================
    SOC --> DEC


    %% =====================================================
    %% HEADER STYLES
    %% =====================================================
    classDef extHeader fill:#3b1d16,stroke:#fb923c,stroke-width:2px,color:#ffffff,font-size:19px;
    classDef detectHeader fill:#0f2a1d,stroke:#4ade80,stroke-width:2px,color:#ffffff,font-size:19px;
    classDef analysisHeader fill:#083344,stroke:#22d3ee,stroke-width:2px,color:#ffffff,font-size:19px;
    classDef outcomeHeader fill:#312e81,stroke:#c084fc,stroke-width:2px,color:#ffffff,font-size:19px;

    class EH extHeader;
    class TH detectHeader;
    class AH analysisHeader;
    class OH outcomeHeader;


    %% =====================================================
    %% NODE STYLES
    %% =====================================================
    classDef external fill:#3b1d16,stroke:#fb923c,stroke-width:2px,color:#ffffff,font-size:18px;
    classDef dns fill:#172554,stroke:#60a5fa,stroke-width:2px,color:#ffffff,font-size:18px;

    classDef splunk fill:#052e16,stroke:#4ade80,stroke-width:3px,color:#ffffff,font-size:18px;
    classDef detection fill:#2e1065,stroke:#c084fc,stroke-width:2px,color:#ffffff,font-size:18px;

    classDef ai fill:#581c87,stroke:#e879f9,stroke-width:2px,color:#ffffff,font-size:18px;
    classDef analyst fill:#083344,stroke:#22d3ee,stroke-width:3px,color:#ffffff,font-size:18px;

    classDef decision fill:#1f2937,stroke:#f8fafc,stroke-width:3px,color:#ffffff,font-size:18px;

    classDef benign fill:#052e16,stroke:#22c55e,stroke-width:2px,color:#ffffff,font-size:18px;
    classDef incident fill:#450a0a,stroke:#f87171,stroke-width:2px,color:#ffffff,font-size:18px;
    classDef evidence fill:#172033,stroke:#38bdf8,stroke-width:2px,color:#ffffff,font-size:18px;


    %% =====================================================
    %% APPLY NODE CLASSES
    %% =====================================================
    class ADV external;
    class R53 dns;

    class SPL splunk;
    class DET detection;

    class AI ai;
    class SOC analyst;

    class DEC decision;

    class AUTH,CLOSE benign;
    class IR incident;
    class CORR,FINAL evidence;


    %% =====================================================
    %% CONTAINER STYLES
    %% =====================================================
    style TOP fill:none,stroke:none

    style EXT fill:#0d1117,stroke:#fb923c,stroke-width:1px
    style DETECT fill:#0d1117,stroke:#4ade80,stroke-width:1px
    style ANALYZE fill:#0d1117,stroke:#22d3ee,stroke-width:1px

    style OUTCOME fill:#0d1117,stroke:#c084fc,stroke-width:1px

    style CASES fill:#111827,stroke:#30363d,stroke-width:1px
    style CASE1 fill:#0d1f17,stroke:#22c55e,stroke-width:1px
    style CASE2 fill:#211111,stroke:#f87171,stroke-width:1px


    %% =====================================================
    %% EDGE STYLE
    %% =====================================================
    linkStyle default stroke:#a8b3c2,stroke-width:2.5px
```

> The scenario uses the shared DNSentinel infrastructure but keeps the project view focused on the actual attack → detection → investigation → response path.

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

## 🔄 SOC Lifecycle & Implementation Reality

| Stage | State |
|---|---|
| **Simulate** | ✅ |
| **Observe** | ✅ |
| **Detect** | ✅ |
| **AI Assist** | ✅ |
| **SOC Investigate** | ✅ |
| **IR Decide** | ✅ |
| **Verify** | ✅ |
| **Document** | ✅ |

> [!IMPORTANT]
> ✅ means supported by implemented project evidence. 🟡 means design/infrastructure/documentation exists but the scenario stage is not complete. ⚪ means planned and is **not presented as implemented**.

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

## 🖼️ Evidence Highlights

<table>
<tr>
<td width="50%"><img src="screenshots/soc/case-01/Case-01_E01_Production-Detection-Triggered.png" alt="Case 01 detection"><br/><sub><b>Case 01:</b> real alert, authorized activity.</sub></td>
</tr>
<tr>
<td width="50%"><img src="screenshots/soc/case-02/Case-02_E03_Activity-Timeline.png" alt="Case 02 timeline"><br/><sub><b>Case 02:</b> staged DNS reconnaissance timeline.</sub></td>
<td width="50%"><img src="screenshots/ir/case-02/IR-E05b_VPC-Flow-Client-Correlation.png" alt="IR network correlation"><br/><sub><b>IR:</b> network correlation without over-attribution.</sub></td>
</tr>
</table>

Full role- and case-organized evidence is available in [`screenshots/`](screenshots/) and [`evidence/`](evidence/).

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

## 📋 Scenario at a Glance

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

## 🧾 Final Outcomes

| Case | Detection result | Human conclusion | Final action |
|---|---|---|---|
| **Case 01** | `16 queries / 4 names / 4 types` | **Authorized / Benign True Positive** | Closed by SOC; no IR escalation |
| **Case 02** | `53 queries / 17 names / 6 types` | **True Positive — Suspicious / Likely Unauthorized DNS Reconnaissance** | Escalated to IR; **Preserve + Monitor — No Active Containment** |

> [!IMPORTANT]
> `observed_dns_source` is the resolver/source address seen by Route 53 authoritative logging. It is not automatically the original endpoint that initiated the DNS lookup. That distinction influenced both SOC attribution and the IR containment decision.

---

## 🔄 How the Exercise Unfolded

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

## 🛠️ Detection Engineering Foundation

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

![Scenario 01 investigation dashboard](screenshots/detection-engineering/04-dns-investigation-dashboard.png)

*The final Splunk Dashboard Studio view connects high-level DNS behavior with source-aware pivots and raw events.*

Read the full engineering story in [`detection-engineering/DETECTION-ENGINEERING.md`](detection-engineering/DETECTION-ENGINEERING.md).

---

## 🟢 Case 01 — The Alert Was Real, but the Activity Was Authorized

Case 01 came from the defender-owned `dns-soc-web01` asset. A post-change DNS validation sequence generated a concentrated authoritative-DNS pattern that genuinely crossed Detection v1.0.

![Authorized DNS validation batch](screenshots/attacker/case-01/01-authorized-dns-validation-batch.png)

*The operational validation checked four names across A, AAAA, TXT and CNAME directly against the authoritative path.*

SOC then proved the activity belonged to a known lab asset and obtained authorization context. The correct conclusion was therefore **Authorized / Benign True Positive**, not “false positive.”

![Case 01 detection result](screenshots/soc/case-01/Case-01_E01_Production-Detection-Triggered.png)

*The detection correctly identified reconnaissance-like behavior; business and asset context changed the disposition.*

Deep case record: [`soc/case-01-soc-investigation-closure.md`](soc/case-01-soc-investigation-closure.md)

---

## 🟠 Case 02 — External DNS Reconnaissance

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

## 🛡️ Incident Response — Confirm the Behavior, Do Not Over-Attribute It

Lubaba did not simply accept the SOC conclusion. IR independently reproduced the core counts, validated the seven-day baseline, expanded the DNS timeline, correlated Nginx and VPC Flow evidence, compared peer DNS sources, and reviewed the Route 53 public hosted-zone records.

![VPC Flow correlation](screenshots/ir/case-02/IR-E05b_VPC-Flow-Client-Correlation.png)

*VPC Flow telemetry corroborated a web connection seen by Nginx, but the web client could not be proven to be the same original endpoint behind the Route 53-observed DNS source.*

The case remained scoped to **Reconnaissance**. No exploitation, persistence, C2 or impact was established.

Final IR decision:

> **PRESERVE + MONITOR ONLY — NO ACTIVE CONTAINMENT**

The decision was intentional: the Route 53 source was resolver-side evidence, no malicious web progression was proven, and the public DNS records were required or benign. Blocking an unproven endpoint would have created attribution and availability risk without an evidence-backed security benefit.

Read [`ir/INCIDENT-RESPONSE.md`](ir/INCIDENT-RESPONSE.md) and [`ir/case-02-validation.md`](ir/case-02-validation.md).

---

## 🤖 AI Was Assistance, Not Authority

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

## 🌐 Network and Protocol View

| Layer / view | Scenario 01 evidence |
|---|---|
| **Layer 7 — DNS** | query name, query type, response code, authoritative behavior, NXDOMAIN pattern |
| **Layer 4** | UDP DNS; later TCP 80/443 seen in supporting web/network evidence |
| **Layer 3** | Route 53-observed resolver/source values and VPC Flow source/destination correlation |
| **Application** | Nginx method, URI, HTTP status, user agent |
| **Cloud** | Route 53 hosted-zone records, CloudTrail/SSM asset attribution for Case 01 |
| **SIEM / AI** | Splunk alert, dashboard, raw events, AI enrichment and analyst validation |

---

## 🗂️ Repository Navigation

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

## ✅ Scenario Completion Record

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

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

## 🧠 Security Engineering Skills Demonstrated

| Skill area | Scenario evidence / design focus |
|---|---|
| **DNS Analysis** | Authoritative query names/types, response patterns, NXDOMAIN and resolver/source semantics |
| **Detection Engineering** | Baseline, hunting SPL, detection v1.0, validation, scheduled alert and dashboard |
| **SOC Investigation** | Source attribution, 5W1H, timeline, peer comparison and disposition |
| **Incident Response** | Independent validation, cross-telemetry scope and proportional response |
| **Network Correlation** | Nginx + VPC Flow + AWS asset context |
| **AI-Assisted SOC** | Structured AI output checked against raw evidence |

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

## 📚 Documentation Model

This scenario repository is a **case/execution layer** built on the shared [DNS Lab Infrastructure](https://github.com/DNSentinel-Lab/DNS-Lab-Infrastructure). It intentionally separates:

- **Design / prerequisites** — what must exist before the exercise;
- **Simulation / ground truth** — what the authorized operator actually generated;
- **Detection Engineering** — baseline, hunting, tuned detection and validation;
- **SOC investigation** — defender-visible evidence and human disposition;
- **IR / containment** — independently justified response and verification;
- **Evidence** — screenshots and structured artifacts that prove the final claims.

> [!NOTE]
> Planned work stays labelled as planned. This repository does not create fake screenshots, fake SPL results, fake ML metrics or fake incident outcomes to make a scenario look complete.

<div align="center">

### DNSentinel Lab
**Build the telemetry. Prove the detection. Investigate the evidence. Verify the response.**

[🏗️ Infrastructure](https://github.com/DNSentinel-Lab/DNS-Lab-Infrastructure) · [**🔎 Scenario 01**](https://github.com/DNSentinel-Lab/Scenario-01-DNS-Recon) · [🧬 Scenario 02](https://github.com/DNSentinel-Lab/Scenario-02-DGA) · [🔄 Scenario 03](https://github.com/DNSentinel-Lab/Scenario-03-Fast-Flux) · [🛰️ Scenario 04](https://github.com/DNSentinel-Lab/Scenario-04-DNS-Tunneling)

[⬆ Back to top](#top)

</div>

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=0,2,12,20,24&height=120&section=footer" width="100%" alt="footer" />
