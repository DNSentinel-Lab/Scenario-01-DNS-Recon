<a id="top"></a>

> 🧭 [Scenario 01](../README.md) › [Exercise](README.md) › **Scenario 01 Realistic Exercise Protocol**

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=flat-square)
![DNSentinel](https://img.shields.io/badge/DNSentinel-Technical_Record-00F5FF?style=flat-square)

---

# Scenario 01 Realistic Exercise Protocol

**Status:** ✅ Used for the completed Scenario 01 execution

This protocol defines the information separation and execution gates used to keep Scenario 01 realistic while remaining safe and reproducible.

## Principle

The defender should reach a conclusion from **defender-visible evidence**, not from knowledge of what the Project Lead intended.

```text
Project Lead / Adversary ground truth
              │
              │ kept separate during investigation
              ▼
Real public DNS behavior → Route 53 / Splunk → SOC → IR
```

## Role boundaries

### Project Lead / Adversary

Knows:

- case purpose;
- attacker/source environment;
- commands;
- UTC timing;
- ground truth.

Does not provide that information to SOC or IR before defender decisions are recorded.

### Detection Engineer

Freezes Detection v1.0 before live execution. The rule is not changed to fit the live result.

### SOC Analyst

Receives:

- production alert;
- dashboard;
- raw defender telemetry;
- asset/business context;
- AI enrichment.

Does not use attacker ground truth to decide disposition.

### Incident Responder

Receives the SOC handoff and evidence. IR independently validates the important facts before choosing a response.

## Official cases

### Case 01

Authorized, defender-owned DNS validation activity capable of producing reconnaissance-like telemetry.

Purpose: prove that business context can turn a correct detection into an **Authorized / Benign True Positive**.

### Case 02

External DNS reconnaissance from a separate AWS account/network.

Purpose: test whether the detection, SOC workflow and IR process can identify and scope real public reconnaissance without relying on attacker-side telemetry.

## Execution gates

```text
Gate 1 — Detection Engineering complete
Gate 2 — Detection v1.0 frozen
Gate 3 — telemetry healthy
Gate 4 — attacker/controller ground truth prepared
Gate 5 — activity generated
Gate 6 — SOC disposition recorded
Gate 7 — IR decision recorded where escalation occurs
Gate 8 — final perspective comparison
```

## Evidence rule

Attacker-side screenshots are ground-truth/supporting execution evidence. SOC and IR conclusions must be supported by defender-side evidence.

The final comparison can use both perspectives after the defender record is complete.

## Safety boundary

Scenario 01 is reconnaissance only. It does not include exploitation, credential attacks, destructive actions, persistence, malware, denial of service or unrelated targets.

## Completed outcome

- Case 01 → Authorized / Benign True Positive → SOC closure.
- Case 02 → Suspicious / Likely Unauthorized DNS Reconnaissance → IR.
- IR → confirmed reconnaissance, no proven progression → Preserve + Monitor / No Active Containment.

---

<div align="center">

[🏠 Scenario Home](../README.md) · [📁 Exercise](README.md) · [⬆ Back to top](#top)

<sub>DNSentinel Lab · Evidence-first DNS security engineering</sub>

</div>
