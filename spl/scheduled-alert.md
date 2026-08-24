<a id="top"></a>

> 🧭 [Scenario 01](../README.md) › [Spl](README.md) › **Scheduled Alert — Scenario 01 Possible DNS Reconnaissance**

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=flat-square)
![DNSentinel](https://img.shields.io/badge/DNSentinel-Technical_Record-00F5FF?style=flat-square)

---

# Scheduled Alert — Scenario 01 Possible DNS Reconnaissance

**Status:** **✅ Implemented and validated**  
**Detection:** [`detection.spl`](detection.spl)  
**Detection version:** `1.0`

## Configuration

| Setting | Final value |
|---|---|
| Alert name | `Scenario 01 - Possible DNS Reconnaissance` |
| Description | `Detects concentrated DNS reconnaissance behavior against the controlled lab namespace. MITRE T1590.002.` |
| Type | Scheduled |
| Cron | `* * * * *` |
| Earliest | `-3m@m` |
| Latest | `now` |
| UI time range | Last 3 minutes |
| Trigger condition | Number of Results is greater than `0` |
| Trigger frequency | Once |
| Throttle | Enabled, 60 seconds |
| Severity | Medium |
| Actions | Add to Triggered Alerts + Webhook |
| Webhook destination | `http://dns-soc-ai-bridge:5000/splunk-webhook` |
| Owner / permissions in final deployed view | `admin` / Private |

## Why this schedule was selected

Fresh Route 53 events were normally searchable within tens of seconds / approximately one to two minutes. A three-minute overlapping lookback gives the scheduler room for normal delivery variation while the 60-second throttle reduces duplicate notifications from overlapping windows.

## Trigger evidence

![Scheduled alert trigger history](../screenshots/detection-engineering/09-scheduled-alert-triggered.png)

The alert fired automatically on a fresh controlled source/window and preserved the expected evidence row.

## Analyst-ready result

The human-facing evidence includes:

```text
detection_name / detection_version
first_event_display / last_event_display
observed_dns_source
query_count
unique_names
distinct_query_types
query_types
query_name_samples
response_codes
nxdomain_count
mitre_technique
severity
rationale
```

The same row also carries the bridge contract:

```text
alert_id
alert_name
scenario
severity
event_time
source
evidence_json
```

## Drilldown / raw evidence

The alert summary is never the only evidence. The raw Route 53 pivot in [`hunting.spl`](hunting.spl) lets the analyst recover the exact DNS events by source/time window.

![Raw-event drilldown](../screenshots/detection-engineering/10-raw-event-drilldown.png)

## AI action boundary

The Webhook forwards the structured alert to the shared AI bridge. The LLM does not determine whether the detection fires; it receives the already-fired alert and returns analyst assistance.

---

<div align="center">

[🏠 Scenario Home](../README.md) · [📁 Spl](README.md) · [⬆ Back to top](#top)

<sub>DNSentinel Lab · Evidence-first DNS security engineering</sub>

</div>
