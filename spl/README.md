# SPL Workspace — Scenario 01 DNS Reconnaissance & Enumeration

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
