<a id="top"></a>

> 🧭 [Scenario 01](../README.md) › [Ai](README.md) › **Scenario 01 AI Mapping — `dns_recon_v1`**

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=flat-square)
![DNSentinel](https://img.shields.io/badge/DNSentinel-Technical_Record-00F5FF?style=flat-square)

---

# Scenario 01 AI Mapping — `dns_recon_v1`

**Status:** **✅ End-to-end validated**

The Scenario 01 AI step begins only after detection v1.0 has already fired. It consumes a structured subset of the same evidence available to the human analyst.

## Scenario identity

```text
scenario_id   = scenario-01-dns-recon
scenario_name = DNS Reconnaissance & Enumeration
ai_profile    = dns_recon_v1
```

## Native Splunk alert contract

The final detection emits these bridge-compatible fields first:

```text
alert_id
alert_name
scenario
severity
event_time
source
evidence_json
```

![Scenario 01 alert evidence contract](../screenshots/detection-engineering/11-ai-alert-evidence-contract.png)

### Field mapping

| Detection / result field | Bridge meaning |
|---|---|
| `alert_id` | Stable unique alert/result identifier |
| `alert_name` | Scenario 01 detection name |
| `scenario` | Human-readable scenario name |
| `severity` | Detection priority (`medium`) |
| `event_time` | First event timestamp in ISO-8601 UTC |
| `source` | `observed_dns_source` |
| `evidence_json` | Serialized structured DNS evidence consumed by the adapter |

## Evidence carried inside `evidence_json`

```text
scenario_id
ai_profile
detection_version
observed_dns_source
query_count
unique_names
distinct_query_types
query_types
query_name_samples
response_codes
nxdomain_count
mitre_technique
rationale
```

The payload describes the evidence. It does not label the source as a confirmed attacker and does not instruct the model to return a predetermined verdict.

## Bridge normalization

The shared bridge accepts either its common direct alert contract or Splunk's native webhook envelope.

For the Splunk envelope, the adapter reads the first result row and normalizes:

```text
result.alert_id      → alert_id
result.alert_name    → alert_name
result.scenario      → scenario
result.severity      → severity
result.event_time    → event_time
result.source        → source
result.evidence_json → evidence object
```

This mapping fixed the earlier HTTP 400 schema validation failure.

## End-to-end validation

A fresh controlled `bridge-final*` DNS burst produced:

```text
20 queries
4 unique names
5 record types
NXDOMAIN response context
```

The scheduled alert fired, the bridge accepted the schema, the OpenAI request returned **HTTP 200**, and the structured response was posted back to Splunk HEC.

The event appeared in:

```text
index=dns_soc_ai
sourcetype=dns_soc:ai:triage
```

![AI result indexed in Splunk](../screenshots/detection-engineering/12-ai-triage-indexed.png)

## AI result shape

The indexed event is nested under `alert.*` and `ai.*` and includes fields such as:

```text
ai.summary
ai.confidence
ai.observed_indicators{}
ai.suspicion_reasons{}
ai.mitre_attack.*
ai.cyber_kill_chain.*
ai.missing_evidence{}
ai.response_considerations{}
human_validation_required
processed_at
request_id
```

## Analyst extraction SPL

```spl
index=dns_soc_ai
| spath path=alert.alert_id output=alert_id
| spath path=alert.alert_name output=alert_name
| spath path=alert.scenario output=scenario
| spath path=alert.severity output=severity
| spath path=ai.summary output=summary
| spath path=ai.confidence output=confidence
| spath path=ai.observed_indicators{} output=observed_indicators
| spath path=ai.suspicion_reasons{} output=suspicion_reasons
| spath path=ai.mitre_attack.tactic output=mitre_tactic
| spath path=ai.mitre_attack.technique_id output=mitre_technique_id
| spath path=ai.mitre_attack.technique_name output=mitre_technique_name
| spath path=ai.cyber_kill_chain.stage output=kill_chain_stage
| spath path=ai.missing_evidence{} output=missing_evidence
| spath path=ai.response_considerations{} output=response_considerations
| eval observed_indicators=mvjoin(observed_indicators," | ")
| eval suspicion_reasons=mvjoin(suspicion_reasons," | ")
| eval missing_evidence=mvjoin(missing_evidence," | ")
| eval response_considerations=mvjoin(response_considerations," | ")
| eval mitre_tactic=mvindex(mitre_tactic,0)
| eval mitre_technique_id=mvindex(mitre_technique_id,0)
| eval mitre_technique_name=mvindex(mitre_technique_name,0)
| eval kill_chain_stage=mvindex(kill_chain_stage,0)
| sort - _time
| table _time alert_id alert_name scenario severity summary confidence observed_indicators suspicion_reasons mitre_tactic mitre_technique_id mitre_technique_name kill_chain_stage missing_evidence response_considerations human_validation_required processed_at request_id
```

## Human validation boundary

The official Scenario 01 exercise still requires the SOC Analyst to compare AI assistance with the original Route 53 evidence and record where the model was correct, incomplete or wrong.

---

<div align="center">

[🏠 Scenario Home](../README.md) · [📁 Ai](README.md) · [⬆ Back to top](#top)

<sub>DNSentinel Lab · Evidence-first DNS security engineering</sub>

</div>
