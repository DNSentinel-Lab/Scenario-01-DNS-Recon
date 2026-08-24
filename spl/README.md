<a id="top"></a>
<img src="https://capsule-render.vercel.app/api?type=soft&color=gradient&customColorList=0,2,12,20,24&height=135&section=header&text=%F0%9F%94%8E%20SPL%20Workspace&fontSize=28&fontColor=ffffff&animation=fadeIn&desc=Scenario%2001%20%E2%80%94%20DNS%20Reconnaissance&descSize=14&descAlignY=68&descColor=00F5FF" width="100%" alt="🔎 SPL Workspace" />

<div align="center">

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=flat-square)
![Workspace](https://img.shields.io/badge/Workspace-SPL_Workspace-00B8D9?style=flat-square)

[🏠 Scenario Home](../README.md) · [🏗️ Shared Infrastructure](https://github.com/DNSentinel-Lab/DNS-Lab-Infrastructure) · [🗂️ All Scenario Repositories](https://github.com/orgs/DNSentinel-Lab/repositories)

</div>

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

**Status:** **✅ Complete**  
**Detection Engineer:** [Sonia](https://github.com/sonia11mansha415)

This folder preserves the four SPL categories required by the shared scenario standard plus the final scheduled-alert configuration.

```text
spl/
├── baseline.spl
├── hunting.spl
├── detection.spl
├── validation.spl
└── scheduled-alert.md
```

## Data source

```text
index=dns_soc_aws
sourcetype=aws:kinesis
```

## Artifacts

### [`baseline.spl`](baseline.spl)

Summarizes ordinary source/window behavior before controlled testing. The observed baseline used for threshold development contained 97 source/windows with maximums of 15 queries and 3 unique names. Record-type diversity reached 8 in background activity, proving diversity alone was not sufficient.

### [`hunting.spl`](hunting.spl)

Contains two standalone hunts:

1. source/window DNS behavior summary;
2. raw DNS evidence pivot.

The hunts intentionally have no detection threshold.

### [`detection.spl`](detection.spl)

Final production detection `v1.0`.

Core condition:

```text
query_count >= 16
AND unique_names >= 4
AND distinct_query_types >= 3
```

`NXDOMAIN` remains evidence context, not a required condition.

The final output also contains the stable fields used by the shared AI bridge.

### [`validation.spl`](validation.spl)

Labels every source/window as:

```text
WOULD DETECT
BELOW THRESHOLD
```

This allows the engineer to compare current behavior with detection v1.0 without editing the production rule.

### [`scheduled-alert.md`](scheduled-alert.md)

Records the final schedule, lookback, trigger, throttle, actions and evidence contract.

## Engineering rule

Thresholds in this folder came from the real lab baseline and controlled tests. They are not presented as universal DNS-security thresholds and should be re-baselined if the environment or traffic profile changes materially.

## Operational result

Detection v1.0 was frozen before the final exercise and used unchanged for both official cases. It correctly surfaced the authorized Case 01 behavior for human contextual closure and the suspicious Case 02 reconnaissance for SOC escalation to IR.

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

<div align="center">

**DNSentinel Scenario 01 · DNS Reconnaissance**

[🏠 Scenario Home](../README.md) · [🏗️ Infrastructure](https://github.com/DNSentinel-Lab/DNS-Lab-Infrastructure) · [⬆ Back to top](#top)

</div>

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=0,2,12,20,24&height=75&section=footer" width="100%" alt="footer" />
