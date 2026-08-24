<a id="top"></a>

> 🧭 [Scenario 01](../README.md) › [Soc](README.md) › **Scenario 01 SOC Investigation Template**

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=flat-square)
![DNSentinel](https://img.shields.io/badge/DNSentinel-Technical_Record-00F5FF?style=flat-square)

---

# Scenario 01 SOC Investigation Template

## Alert identity

| Field | Value |
|---|---|
| Analyst | Musfira |
| Alert ID | |
| Alert time UTC | |
| Severity | |
| MITRE | `T1590.002` |
| Cyber Kill Chain | Reconnaissance |

## 5W1H

| Question | Notes |
|---|---|
| **Who?** | Observed source/resolver identity; ownership if actually known |
| **What?** | DNS behavior observed |
| **When?** | First seen / last seen / alert time |
| **Where?** | Route 53 child zone; any Web/VPC follow-up |
| **Why suspicious?** | Evidence-backed reason; comparison with baseline |
| **How?** | Names, record types, frequency, responses and sequence |

## Detection evidence

| Metric | Value |
|---|---|
| Observed DNS source | |
| Query count | |
| Unique names | |
| Distinct query types | |
| Query types | |
| Response codes | |
| NXDOMAIN count | |

## Supporting evidence

- Route 53 raw-event search:
- Dashboard screenshot/link:
- Historical-source search:
- Nginx follow-up:
- VPC Flow follow-up:
- Other context:

## AI validation

| Check | Result |
|---|---|
| Facts accurate | Yes / Partial / No |
| Reasoning supported | Yes / Partial / No |
| MITRE/kill-chain useful | Yes / Partial / No |
| Unsupported assumptions found | |
| Overall AI assessment | Correct / Partially correct / Incorrect |

## SOC disposition

Select one:

- [ ] TP — suspicious/unauthorized reconnaissance
- [ ] TP — real behavior but verified authorized/benign context
- [ ] FP — normal/non-recon behavior incorrectly matched the detection
- [ ] Inconclusive — more context required

**Confidence:** Low / Medium / High

**Evidence-backed conclusion:**


## IR handoff

- Escalated: Yes / No
- Time escalated UTC:
- Reason:
- What remains unknown:
- Evidence references:

## Post-ground-truth comparison

Complete only after the Project Lead reveals attacker ground truth.

- Did SOC identify the correct time window?
- Did SOC identify the observable source correctly?
- Did SOC reconstruct the queried names/types?
- Did SOC avoid unsupported attribution?
- Did AI agree with the raw evidence?
- Was the final disposition correct?
- Detection feedback for Sonia:

---

<div align="center">

[🏠 Scenario Home](../README.md) · [📁 Soc](README.md) · [⬆ Back to top](#top)

<sub>DNSentinel Lab · Evidence-first DNS security engineering</sub>

</div>
