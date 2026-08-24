# Screenshot Evidence — Scenario 01

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
