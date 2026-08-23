# Detection Engineering Validation Record — Scenario 01

**Detection Engineer:** [Sonia](https://github.com/sonia11mansha415)  
**Detection:** `Scenario 01 - Possible DNS Reconnaissance`  
**Version:** `1.0`  
**MITRE:** `T1590.002`  
**Engineering result:** **PASS**

This record summarizes the evidence used to declare the Scenario 01 Detection Engineering phase ready.

## Acceptance matrix

| Gate | Expected | Observed | Result |
|---|---|---|---|
| Route 53 field mapping | Real authoritative DNS fields can be extracted | Query name/type, response, protocol, edge, source and EDNS context mapped | ✅ PASS |
| Source semantics | Avoid unsupported attacker attribution | `observed_dns_source` kept neutral | ✅ PASS |
| Current ingestion timing | Understand searchability before scheduling | Fresh delivery generally tens of seconds / within ~1–2 min | ✅ PASS |
| Backlog analysis | Separate anomalies from normal delivery | Two older ~3.5h backlog windows identified | ✅ PASS |
| Baseline | Measure normal behavior before threshold | 97 source/windows characterized | ✅ PASS |
| Dashboard | Analyst can investigate DNS behavior | Final Dashboard Studio view operational | ✅ PASS |
| Hunting | Summary + raw pivot available | Two hunts validated | ✅ PASS |
| Initial detection against baseline | No high-confidence baseline result | 0 result rows after threshold | ✅ PASS |
| Controlled positive | Recon-like behavior should trigger | 20 queries / 4 names / 5 types detected | ✅ PASS |
| Benign/basic DNS | Should stay below rule | 0 detection rows | ✅ PASS |
| Detection v1.0 | Clean analyst-ready row | Final fields and rationale populated | ✅ PASS |
| Validation SPL | Show both sides of boundary | `WOULD DETECT` / `BELOW THRESHOLD` works | ✅ PASS |
| Scheduled alert | Fires automatically | Trigger history recorded | ✅ PASS |
| Drilldown | Raw authoritative events recoverable | Raw Route 53 events verified | ✅ PASS |
| AI contract | Bridge-required fields present | `alert_id`…`evidence_json` populated | ✅ PASS |
| AI processing | Bridge accepts alert and calls model | OpenAI HTTP 200 observed | ✅ PASS |
| HEC return | Structured AI result returns to Splunk | Event present in `dns_soc_ai` | ✅ PASS |

## Baseline evidence used for threshold selection

24-hour source/window summary:

```text
source_windows           = 97
median_queries           = 1
p95_queries              = 6
max_queries              = 15
median_unique_names      = 1
p95_unique_names         = 2
max_unique_names         = 3
median_query_types       = 1
p95_query_types          = 6
max_query_types          = 8
```

Interpretation:

- normal/background DNS is usually very small;
- some background windows still contain many record types;
- record-type diversity alone is insufficient;
- query concentration + name breadth + type diversity provide better separation.

## Final detection boundary

```text
query_count >= 16
unique_names >= 4
distinct_query_types >= 3
```

The boundary is specific to the measured lab dataset. It should be re-baselined if the telemetry profile changes materially.

## Positive engineering test

Controlled sequence:

```text
20 queries
4 unique test names
A, AAAA, MX, NS, TXT
```

Expected: detection row.  
Observed: detection row(s) with the expected source, count, name breadth and type diversity.  
Result: **PASS**.

## Benign / false-positive test

Basic authorized DNS lookups used only ordinary A/AAAA behavior and remained below the final conditions.

Expected: no detection row.  
Observed: no detection row.  
Result: **PASS**.

## Scheduled alert evidence

Final alert:

```text
cron:            * * * * *
lookback:        Last 3 minutes
trigger:         Number of Results > 0
trigger mode:    Once
throttle:        60 seconds
actions:         Triggered Alerts + Webhook
```

The automatic trigger was confirmed in Splunk and the result could be opened for investigation.

## AI integration evidence

Final bridge-compatible result fields:

```text
alert_id
alert_name
scenario
severity
event_time
source
evidence_json
```

A fresh validation alert proceeded through the Webhook and shared bridge, reached OpenAI successfully and returned a structured event to:

```text
index=dns_soc_ai
sourcetype=dns_soc:ai:triage
```

The AI output remains advisory and carries `human_validation_required=true`.

## Known implementation observations

### Minute-boundary aggregation

A very short controlled burst can straddle one-minute buckets and therefore appear in more than one aggregate row. This is expected from `bin _time span=1m`; alert lookback/throttling is used operationally rather than pretending the boundary does not exist.

### Ingestion backlog outliers

Historical multi-hour delays were treated as ingestion anomalies rather than as the normal scheduler target.

### Public authoritative source identity

`observed_dns_source` is not automatically the original endpoint or a confirmed attacker.

## Engineering completion decision

**Scenario 01 Detection Engineering: COMPLETE / READY FOR OFFICIAL EXERCISE.**

This decision covers the detection-engineering lifecycle only. It does not mark SOC final disposition, IR, containment or final scenario verification complete.
