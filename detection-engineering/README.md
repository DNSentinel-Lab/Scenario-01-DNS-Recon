<a id="top"></a>
<img src="https://capsule-render.vercel.app/api?type=soft&color=gradient&customColorList=0,2,12,20,24&height=145&section=header&text=%F0%9F%9B%A0%EF%B8%8F%20Detection%20Engineering%20Workspace&fontSize=28&fontColor=ffffff&animation=fadeIn&desc=Scenario%2001%20%E2%80%94%20DNS%20Reconnaissance%20%26%20Enumeration&descSize=14&descAlignY=69&descColor=00F5FF" width="100%" alt="🛠️ Detection Engineering Workspace" />

<div align="center">






<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=16&duration=2600&pause=850&color=00F5FF&center=true&vCenter=true&repeat=true&width=1000&height=64&lines=Telemetry+%E2%86%92+Baseline+%E2%86%92+Hunt+%E2%86%92+Detect+%E2%86%92+Validate;Dashboard+%E2%86%92+Scheduled+Alert+%E2%86%92+Raw+Evidence+%E2%86%92+AI+Assist;Build+what+the+analyst+can+verify+from+evidence" alt="Detection Engineering lifecycle" />

🏠 Scenario Home · 📊 Dashboard · 🔎 SPL Workspace · 🤖 AI Mapping · 🖼️ Evidence

</div>

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

Detection Engineer: Sonia
Status: ✅ Detection Engineering complete
Primary MITRE ATT&CK: T1590.002 — Gather Victim Network Information: DNS
Production rule: Scenario 01 - Possible DNS Reconnaissance · v1.0

This workspace records how Sonia turned real Route 53 authoritative DNS telemetry into a detection the SOC could actually investigate and trust. The work moved through field mapping, ingestion timing, baselining, dashboard engineering, hunting, threshold design, positive and benign validation, scheduled alerting, raw-event drilldown and the Scenario 01 AI evidence path.

[!NOTE]
This was Sonia's first end-to-end Detection Engineering assignment. The final rule was not copied from a generic threshold: it was built from the lab's own telemetry, tested against both sides of the boundary, and frozen before the official Scenario 01 exercise.

🚦 Start here

Artifact

What it contains

DETECTION-ENGINEERING.md

Flagship engineering story — the complete journey from raw DNS telemetry to analyst-ready alerting, including the important troubleshooting and lessons learned.

detection-engineering-validation.md

Acceptance record — the validation matrix used to declare Detection Engineering ready for the official exercise.

🔁 Engineering path

flowchart LR
    A["Route 53 Telemetry"] --> B["Field Mapping"]
    B --> C["Ingestion Timing"]
    C --> D["Baseline"]
    D --> E["Dashboard + Hunts"]
    E --> F["Detection v1.0"]
    F --> G["Positive + Benign Validation"]
    G --> H["Scheduled Alert"]
    H --> I["Raw Evidence Drilldown"]
    I --> J["AI Evidence Contract"]
    J --> K["SOC-Ready"]

The finish line was not simply "the SPL returned a result." The rule had to survive validation, run automatically, expose useful evidence, preserve a raw-event path, and hand structured context to the shared AI bridge without giving AI decision authority.

🎯 Final detection boundary

query_count >= 16
AND unique_names >= 4
AND distinct_query_types >= 3

NXDOMAIN is preserved as investigation context, but it is not required for the rule to fire.

<details>
<summary><strong>Why these conditions?</strong></summary>
<br/>

The measured baseline reached a maximum of 15 queries and 3 unique names in a source/window. Background activity also reached high record-type diversity, which proved that record-type diversity by itself was too noisy. The final logic therefore combines query concentration, name breadth and type diversity rather than trusting one indicator.

</details>

✅ What was validated

Engineering gate

Result

Route 53 field mapping and neutral source semantics

✅ Complete

Current ingestion timing and backlog analysis

✅ Complete

24-hour normal baseline

✅ Complete

Splunk Dashboard Studio investigation surface

✅ Complete

Hunting summary + raw-event pivot

✅ Complete

Detection v1.0

✅ Complete

Controlled positive test

✅ PASS

Benign / false-positive test

✅ PASS

Reusable validation SPL

✅ Complete

Scheduled alert + trigger history

✅ Validated

Analyst evidence row + raw-event drilldown

✅ Validated

Webhook / AI evidence contract

✅ Validated

OpenAI → HEC → dns_soc_ai return path

✅ Validated

📊 Analyst-facing result



The dashboard was engineered as an investigation surface, not decoration: source-aware DNS KPIs, time behavior, query patterns and raw-event pivots all support the same detection story.

🧩 Connected workspaces

Detection Engineering
├── ../spl/          → baseline, hunts, production detection, validation, scheduled alert
├── ../dashboard/    → analyst investigation surface
├── ../ai/           → Scenario 01 evidence contract and AI mapping
└── ../screenshots/  → curated engineering and troubleshooting evidence

../spl/README.md — final SPL artifacts and rule logic.

../dashboard/README.md — Dashboard Studio implementation and analyst pivots.

../ai/scenario-01-ai-mapping.md — stabilized alert-to-AI evidence contract.

../screenshots/detection-engineering/ — field mapping, baseline, dashboard, hunts, validation, alert and AI-path evidence.

<details>
<summary><strong>🧰 Troubleshooting that shaped the engineering</strong></summary>
<br/>

Three reusable lessons were preserved in the flagship record:

Missing fresh telemetry: the detection was protected while Sonia traced Kinesis checkpoint and Splunk KV Store health instead of rewriting known-good SPL.

Webhook reached the bridge but failed schema validation: transport success was separated from payload-contract success, then the final alert fields were normalized.

AI event existed but the first table looked empty: nested alert.* and ai.* JSON was inspected and extracted correctly rather than treating the producer as broken.

The pattern stayed consistent: prove the working layer → isolate the failing boundary → change one thing → validate recovery.

</details>

🧠 Engineering principle

<div align="center">

A detection is unfinished until an analyst can explain why it fired.

</div>

Raw telemetry = truth source
Detection      = behavioral lead
Dashboard      = investigation surface
AI             = advisory context
Human analyst  = security decision

Detection v1.0 was frozen before the final exercise and then used unchanged for both official Scenario 01 cases: one was closed by SOC as an Authorized / Benign True Positive, while the external reconnaissance case was escalated for independent IR validation.

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

<div align="center">

DNSentinel Scenario 01 · Detection Engineering · Sonia

🏠 Scenario Home · 📖 Full Engineering Story · ✅ Validation Record · ⬆ Back to top

<sub>Evidence-first DNS security engineering · Build → Validate → Operationalize → Hand off</sub>

</div>

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=0,2,12,20,24&height=75&section=footer" width="100%" alt="footer" />
