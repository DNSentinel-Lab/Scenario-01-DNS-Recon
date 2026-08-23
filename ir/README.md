# Incident Response Workspace — Scenario 01 DNS Reconnaissance & Enumeration

**Status:** ⏳ Official blind-exercise IR phase pending  
**IR / Defender:** Lubaba

Detection Engineering is complete and the SOC investigation workflow is prepared. IR begins only when Musfira sends an evidence-backed handoff or when the scenario reaches an approved response checkpoint.

## Role boundary

```text
Detection alert
    ↓
SOC investigation
    ↓
SOC disposition + evidence
    ↓
IR decision
    ↓
approved response
    ↓
verification
```

IR does not receive the attacker's private ground truth before the SOC handoff. This keeps the defender workflow realistic.

## Minimum SOC handoff

IR should receive:

```text
Alert ID
First / last seen UTC
Observed DNS source
Names / query types / frequency
Response and NXDOMAIN context
Supporting Nginx/VPC evidence
MITRE T1590.002
Cyber Kill Chain: Reconnaissance
AI-vs-human validation result
SOC disposition and confidence
What is still unknown
Evidence links/screenshots
```

## Response decision

Scenario 01 is reconnaissance, so containment must be proportionate. Possible responses may include:

- preserve evidence and monitor;
- investigate/validate ownership of an external source;
- restrict an identified source where technically justified;
- reduce unnecessary public DNS metadata/exposure;
- apply a web/network control if follow-up behavior justifies it.

Do not pre-script a containment action simply because the alert fired.

## Verification requirement

If a control is changed, IR must prove the outcome:

```text
Before response → suspicious/undesired behavior visible
Response/change → approved defensive action
After response  → expected technical change observed
Business path   → legitimate service still works where required
```

## AI boundary

AI can suggest response considerations, but it cannot authorize containment. Lubaba must review whether AI suggestions are appropriate to the actual evidence and current scope.

## Final IR record

Document:

- action selected and why;
- exact UTC time;
- system/service changed;
- evidence preserved;
- post-action verification;
- final status;
- any follow-up recommendation.

The actual attacker ground truth is revealed only after the defender decision record has been locked.
