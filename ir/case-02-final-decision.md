# Case 02 Final IR Decision

**Responder:** Lubaba  
**Scenario:** 01 — DNS Reconnaissance & Enumeration  
**Decision:** **PRESERVE + MONITOR ONLY — NO ACTIVE CONTAINMENT**  
**Final status:** Closed after IR validation

## Finding

IR independently confirmed DNS reconnaissance behavior against `soclab.abdul4rehman215.tech`.

The defender evidence established:

- 53 queries across 17 names and six DNS record types;
- staged reconnaissance behavior;
- first-seen Route 53-observed source in the available seven-day data;
- no continued activity from the same observed source in the scoped extended window;
- one later web request, corroborated by VPC Flow, but not proven malicious or linked to the DNS source;
- public DNS records limited to expected/benign content;
- no evidence of exploitation, persistence, command-and-control or impact.

## Attribution boundary

`54.242.155.119` is preserved as the **Route 53-observed DNS source / resolver**.

Available evidence did not justify relabeling it as a confirmed attacker endpoint. Therefore a source block against that value would have been an attribution-driven risk rather than an evidence-backed containment action.

## Response decision

> **Preserve the evidence, continue monitoring, and do not perform active containment.**

### Why

1. Original-client attribution remained unresolved.
2. No malicious HTTP/HTTPS follow-up was established.
3. The public hosted-zone records were required or harmless.
4. The scenario remained limited to reconnaissance.
5. Blocking a resolver/source without endpoint proof could affect legitimate DNS users.

## Business impact

No configuration change was required, so the decision avoided unnecessary availability risk to the public DNS/Web service.

## Verification logic

Because no control was changed, verification focused on the investigation outcome rather than a before/after block:

- Route 53 evidence remained available;
- Nginx and VPC Flow evidence were available for correlation;
- no additional Case 02 DNS activity from the same observed source was found in the scoped extended window;
- the hosted zone remained intact;
- no evidence justified progression beyond Reconnaissance.

## Final classification

```text
Confirmed DNS reconnaissance behavior
+ original endpoint unresolved
+ no proven malicious progression
+ intentional public DNS exposure
= Preserve + Monitor / No Active Containment
```

This decision is part of the Scenario 01 result—not a missing containment step.
