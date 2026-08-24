<a id="top"></a>
<img src="https://capsule-render.vercel.app/api?type=soft&color=gradient&customColorList=0,2,12,20,24&height=135&section=header&text=%F0%9F%96%BC%EF%B8%8F%20Screenshot%20Evidence&fontSize=28&fontColor=ffffff&animation=fadeIn&desc=Scenario%2001%20%E2%80%94%20DNS%20Reconnaissance&descSize=14&descAlignY=68&descColor=00F5FF" width="100%" alt="🖼️ Screenshot Evidence" />

<div align="center">

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=flat-square)
![Workspace](https://img.shields.io/badge/Workspace-Screenshot_Evidence-6F42C1?style=flat-square)

[🏠 Scenario Home](../README.md) · [🏗️ Shared Infrastructure](https://github.com/DNSentinel-Lab/DNS-Lab-Infrastructure) · [🗂️ All Scenario Repositories](https://github.com/orgs/DNSentinel-Lab/repositories)

</div>

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

**Status:** ✅ Curated and organized by role/case

The repository keeps detailed evidence available while the flagship Markdown stories display only the screenshots that move the investigation forward.

## Detection Engineering

Folder: [`detection-engineering/`](detection-engineering/)

Contains field mapping, ingestion timing, baseline, dashboard, hunting, validation, scheduled alert, raw drilldown and AI evidence-path screenshots.

## Project Lead / Adversary

### Case 01

| File | Purpose |
|---|---|
| [`attacker/case-01/01-authorized-dns-validation-batch.png`](attacker/case-01/01-authorized-dns-validation-batch.png) | Final authoritative 4-name × 4-type authorized validation sequence |
| [`attacker/case-01/02-authorized-activity-alert-trigger.png`](attacker/case-01/02-authorized-activity-alert-trigger.png) | Alert trigger history after the authorized activity |

### Case 02

| File | Purpose |
|---|---|
| [`attacker/case-02/01-authority-and-zone-baseline.png`](attacker/case-02/01-authority-and-zone-baseline.png) | NS / SOA authority discovery |
| [`attacker/case-02/02-service-name-a-enumeration.png`](attacker/case-02/02-service-name-a-enumeration.png) | Service-style A-record enumeration |
| [`attacker/case-02/03-service-name-txt-enumeration.png`](attacker/case-02/03-service-name-txt-enumeration.png) | TXT / authority information checks |
| [`attacker/case-02/04-multitype-recon-command.png`](attacker/case-02/04-multitype-recon-command.png) | Additional multi-type recon command pattern |

## SOC Analyst

### Case 01

Folder: [`soc/case-01/`](soc/case-01/)

E01–E10 cover detection, raw DNS, timeline, baseline, cross-telemetry, cloud/asset attribution, AI validation and authorization closure.

### Case 02

Folder: [`soc/case-02/`](soc/case-02/)

E01–E10 cover detection, raw DNS, staged timing, name breadth, response distribution, seven-day history, cross-telemetry, AI validation, unresolved ownership and IR escalation.

## Incident Response — Case 02

Folder: [`ir/case-02/`](ir/case-02/)

The set preserves:

- independent SOC-fact validation;
- seven-day baseline;
- custom extended DNS timeline;
- Nginx initial/raw/parsed/wider-scope views;
- VPC Flow raw parsing and client correlation;
- peer DNS source comparison;
- public-DNS success views;
- Route 53 hosted-zone record review.

## Screenshot rule

One screenshot should prove one important fact, decision or reusable lesson. Construction/progress screenshots and repetitive failed searches are not part of the final Scenario 01 story.

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

<div align="center">

**DNSentinel Scenario 01 · DNS Reconnaissance**

[🏠 Scenario Home](../README.md) · [🏗️ Infrastructure](https://github.com/DNSentinel-Lab/DNS-Lab-Infrastructure) · [⬆ Back to top](#top)

</div>

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=0,2,12,20,24&height=75&section=footer" width="100%" alt="footer" />
