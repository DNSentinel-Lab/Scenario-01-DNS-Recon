# Incident Response Workspace — Scenario 01

**Incident Responder / Defender:** [Lubaba](https://github.com/lubaba1513-pixel)  
**Status:** ✅ Case 02 IR complete  
**Final response:** **Preserve + Monitor — No Active Containment**

Case 01 was closed by SOC as an Authorized / Benign True Positive and did not require Incident Response. Lubaba's Scenario 01 IR work therefore focused on Case 02.

## Start here

- [`INCIDENT-RESPONSE.md`](INCIDENT-RESPONSE.md) — flagship IR story from handoff acceptance through final response.
- [`case-02-validation.md`](case-02-validation.md) — independent acceptance matrix and evidence gallery.
- [`case-02-final-decision.md`](case-02-final-decision.md) — final response decision and reasoning.
- [`../soc/case-02-soc-investigation-ir-handoff.md`](../soc/case-02-soc-investigation-ir-handoff.md) — original SOC handoff.

## IR result at a glance

```text
SOC suspicious DNS reconnaissance
    ↓
IR independently reproduces core facts
    ↓
7-day baseline + extended DNS timeline
    ↓
Nginx + VPC Flow correlation
    ↓
peer-source comparison
    ↓
public DNS exposure review
    ↓
confirmed reconnaissance / no proven progression
    ↓
PRESERVE + MONITOR ONLY
NO ACTIVE CONTAINMENT
```

IR did not treat the Route 53-observed DNS source as a confirmed attacker endpoint and did not force a containment action without a defensible target.
