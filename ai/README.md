<a id="top"></a>
<img src="https://capsule-render.vercel.app/api?type=soft&color=gradient&customColorList=0,2,12,20,24&height=135&section=header&text=%F0%9F%A4%96%20AI%20Integration%20%2F%20Profile&fontSize=28&fontColor=ffffff&animation=fadeIn&desc=Scenario%2001%20%E2%80%94%20DNS%20Reconnaissance&descSize=14&descAlignY=68&descColor=00F5FF" width="100%" alt="🤖 AI Integration / Profile" />

<div align="center">

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=flat-square)
![Workspace](https://img.shields.io/badge/Workspace-AI_Integration_%2F_Profile-7B2CBF?style=flat-square)

[🏠 Scenario Home](../README.md) · [🏗️ Shared Infrastructure](https://github.com/DNSentinel-Lab/DNS-Lab-Infrastructure) · [🗂️ All Scenario Repositories](https://github.com/orgs/DNSentinel-Lab/repositories)

</div>

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

**Status:** **✅ Scenario-specific engineering integration validated**  
**Shared AI foundation:** Reused from `DNS-Lab-Infrastructure`  
**Scenario profile:** `dns_recon_v1`

This folder contains only the Scenario 01 mapping required after the human-facing detection fields stabilized. The shared Flask/OpenAI/HEC platform is not duplicated here.

## Flow

```text
Scenario 01 detection v1.0
      ↓
Scheduled Splunk alert
      ↓
Native webhook result
      ↓
Scenario 01 evidence normalization
      ↓
Shared AI bridge
      ↓
Structured OpenAI response
      ↓
Internal HTTPS HEC
      ↓
index=dns_soc_ai
      ↓
Human analyst validation
```

## Principle

AI is **advisory**.

The detection is created and validated from Route 53 evidence before the LLM is involved. The analyst keeps direct access to the raw Splunk events, and the indexed AI result retains `human_validation_required=true`.

## Scenario-specific artifact

[`scenario-01-ai-mapping.md`](scenario-01-ai-mapping.md) documents:

- Scenario identity;
- the final alert contract;
- `evidence_json` content;
- bridge normalization;
- AI result extraction in Splunk;
- the end-to-end validation result.

## Operational use in the completed exercise

The same AI path was used during the final Scenario 01 SOC workflow. Musfira compared the structured AI result with raw Route 53 evidence in both cases, and Lubaba treated response considerations as advisory during Case 02 IR. AI helped identify missing evidence but did not determine authorization, endpoint attribution or containment.

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" alt="section divider" />

<div align="center">

**DNSentinel Scenario 01 · DNS Reconnaissance**

[🏠 Scenario Home](../README.md) · [🏗️ Infrastructure](https://github.com/DNSentinel-Lab/DNS-Lab-Infrastructure) · [⬆ Back to top](#top)

</div>

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=0,2,12,20,24&height=75&section=footer" width="100%" alt="footer" />
