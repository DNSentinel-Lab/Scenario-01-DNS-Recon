<a id="top"></a>
<img src="https://capsule-render.vercel.app/api?type=soft&color=gradient&customColorList=0,2,12,20,24&height=135&section=header&text=%F0%9F%9B%A1%EF%B8%8F%20Incident%20Response%20Workspace&fontSize=28&fontColor=ffffff&animation=fadeIn&desc=Scenario%2001%20%E2%80%94%20DNS%20Reconnaissance&descSize=14&descAlignY=68&descColor=00F5FF" width="100%" alt="🛡️ Incident Response Workspace" />

<div align="center">

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=flat-square)
![Workspace](https://img.shields.io/badge/Workspace-Incident_Response_Workspace-E5534B?style=flat-square)

[🏠 Scenario Home](../README.md) · [🏗️ Shared Infrastructure](https://github.com/DNSentinel-Lab/DNS-Lab-Infrastructure) · [🗂️ All Scenario Repositories](https://github.com/orgs/DNSentinel-Lab/repositories)

</div>

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

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

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

<div align="center">

**DNSentinel Scenario 01 · DNS Reconnaissance**

[🏠 Scenario Home](../README.md) · [🏗️ Infrastructure](https://github.com/DNSentinel-Lab/DNS-Lab-Infrastructure) · [⬆ Back to top](#top)

</div>

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=0,2,12,20,24&height=75&section=footer" width="100%" alt="footer" />
