<a id="top"></a>
<img src="https://capsule-render.vercel.app/api?type=soft&color=gradient&customColorList=0,2,12,20,24&height=135&section=header&text=%F0%9F%93%8A%20Dashboard%20Workspace&fontSize=28&fontColor=ffffff&animation=fadeIn&desc=Scenario%2001%20%E2%80%94%20DNS%20Reconnaissance&descSize=14&descAlignY=68&descColor=00F5FF" width="100%" alt="📊 Dashboard Workspace" />

<div align="center">

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=flat-square)
![Workspace](https://img.shields.io/badge/Workspace-Dashboard_Workspace-0A84FF?style=flat-square)

[🏠 Scenario Home](../README.md) · [🏗️ Shared Infrastructure](https://github.com/DNSentinel-Lab/DNS-Lab-Infrastructure) · [🗂️ All Scenario Repositories](https://github.com/orgs/DNSentinel-Lab/repositories)

</div>

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

**Status:** **✅ Complete**  
**Engineer:** [Sonia](https://github.com/sonia11mansha415)  
**Format:** Splunk Dashboard Studio JSON

The dashboard is an investigation surface built from the real Route 53 authoritative fields used by Scenario 01.

## Implementation artifact

[`scenario-01-dns-recon.dashboard.json`](scenario-01-dns-recon.dashboard.json)

![Scenario 01 DNS Reconnaissance Investigation](../screenshots/detection-engineering/04-dns-investigation-dashboard.png)

*The Dashboard Studio view gives the analyst source-aware DNS KPIs, time behavior, pattern distributions and raw-event investigation pivots in one page.*

## Data source

Primary panels use:

```text
index=dns_soc_aws
sourcetype=aws:kinesis
```

Real Route 53 fields are extracted from `_raw` and include:

```text
query_name
query_type
response_code
protocol
edge_location
observed_dns_source
edns_client_subnet
```

## Shared controls

| Control | Purpose |
|---|---|
| **Global Time Range** | Applies one investigation window across the dashboard |
| **Observed DNS Source** | Pivots the relevant panels to a selected source/resolver identity; `All` remains available |

## SOC summary

The first row answers the analyst's immediate questions:

- **Distinct Query Types**
- **Observed DNS Sources**
- **Total DNS Queries**
- **Unique Queried Names**
- **NXDOMAIN Count**

## Behavior over time

### DNS Activity Over Time

Shows query concentration and bursts across the selected time range.

### Record-Type Diversity Over Time

Shows how the number of distinct DNS record types changes over time.

## DNS pattern views

### Query-Type Distribution

Answers which DNS record types dominate the selected activity.

### Top Queried Names

Surfaces the most frequently queried names/subdomains.

### Response Distribution

Shows Route 53 result context such as `NOERROR` and `NXDOMAIN`.

## Investigation tables

### DNS Investigation Events

Keeps the raw analyst pivots visible:

```text
_time
observed_dns_source
edns_client_subnet
query_name
query_type
response_code
protocol
edge_location
```

### Top 1-Minute DNS Bursts

Aggregates source/window behavior into:

```text
query_count
unique_names
distinct_query_types
query_types
query_names
```

This provides a quick bridge from the dashboard into the hunting and detection logic.

## Engineering decisions

- The dashboard uses real project telemetry.
- `observed_dns_source` remains neutral.
- NXDOMAIN is displayed as useful response context, not treated as a mandatory reconnaissance feature.
- The final implementation is JSON because this dashboard was built in Dashboard Studio.

## Relationship to the detection

The dashboard and the rule intentionally examine the same behavior from different angles:

```text
Dashboard → investigation and context
Hunting   → flexible analyst pivots
Detection → evidence-based threshold decision
Alert     → analyst-ready result
```

The dashboard was completed before alert operationalization and did not need redesign after detection v1.0 stabilized.

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

<div align="center">

**DNSentinel Scenario 01 · DNS Reconnaissance**

[🏠 Scenario Home](../README.md) · [🏗️ Infrastructure](https://github.com/DNSentinel-Lab/DNS-Lab-Infrastructure) · [⬆ Back to top](#top)

</div>

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=0,2,12,20,24&height=75&section=footer" width="100%" alt="footer" />
