<a id="top"></a>

> 🧭 [Scenario 01](../README.md) › [Exercise](README.md) › **Scenario 01 Final Comparison — Ground Truth vs Defender View**

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=flat-square)
![DNSentinel](https://img.shields.io/badge/DNSentinel-Technical_Record-00F5FF?style=flat-square)

---

# Scenario 01 Final Comparison — Ground Truth vs Defender View

**Status:** ✅ Complete  
**Primary MITRE ATT&CK:** `T1590.002 — Gather Victim Network Information: DNS`  
**Cyber Kill Chain:** Reconnaissance

This comparison connects what actually happened with what each defensive layer could legitimately conclude.

## Case 01 — authorized DNS validation

| Perspective | What it saw / knew | Result |
|---|---|---|
| **Ground truth** | `dns-soc-web01` performed approved post-change DNS validation | Authorized operational activity |
| **Route 53** | Concentrated multi-name, multi-type authoritative DNS burst | Reconnaissance-like behavior visible |
| **Detection v1.0** | `16 queries / 4 names / 4 types` | Correctly triggered |
| **AI** | Possible DNS reconnaissance; missing ownership/authorization context | Useful but incomplete |
| **SOC** | Raw event/timeline + cloud asset context + authorization | **Authorized / Benign True Positive** |
| **IR** | Not required | SOC closure was sufficient |

### Case 01 lesson

A behavioral detection can be technically correct while the underlying activity is legitimate. The final disposition came from asset ownership and authorization evidence—not from changing the DNS facts.

---

## Case 02 — external DNS reconnaissance

| Perspective | What it saw / knew | Result |
|---|---|---|
| **Ground truth** | External Kali in a separate AWS account performed public DNS reconnaissance using authority discovery, record-type checks and service-name enumeration | Real external reconnaissance |
| **Route 53** | Resolver/source-side authoritative DNS events | `53 queries / 17 names / 6 types`; original endpoint not directly established |
| **Detection v1.0** | Concentrated high-breadth/multi-type behavior | Correctly triggered |
| **AI** | Suggested DNS reconnaissance, `T1590.002`, Reconnaissance; left source/authorization/follow-up gaps | Directionally correct, incomplete |
| **SOC** | First-seen staged DNS outlier with no known ownership/authorization | **Suspicious / Likely Unauthorized TP**, High confidence, Medium risk → IR |
| **IR** | Independently reproduced SOC facts; scoped DNS/Nginx/VPC/public exposure | Confirmed reconnaissance; no proven progression |
| **Response** | Attribution remained unresolved; no malicious endpoint/control target established | **Preserve + Monitor — No Active Containment** |

## What the telemetry saw correctly

- public-authoritative DNS reconnaissance was visible;
- the service-name guessing pattern was recoverable;
- record-type diversity and query breadth were recoverable;
- timing showed staged bursts;
- response codes showed the high NXDOMAIN context;
- the defender could correlate later Web/VPC evidence independently.

## What the telemetry could not prove alone

- the original attacker endpoint behind every Route 53 query;
- attacker intent;
- that a later web client was the same actor as the DNS source;
- compromise, execution, persistence, C2 or impact.

## Detection v1.0 assessment

Detection v1.0 successfully identified both:

1. a legitimate source that behaved like reconnaissance;
2. suspicious external reconnaissance.

That is a useful result. It shows why detection output must be followed by analyst context instead of being treated as a verdict.

## AI assessment

AI added value by:

- summarizing the DNS behavior;
- suggesting the correct primary MITRE mapping;
- identifying evidence gaps;
- keeping human validation required.

AI did not establish:

- source ownership;
- authorization;
- endpoint attribution;
- a containment target.

Those remained human decisions.

## SOC assessment

Musfira correctly used the same investigation discipline on two different cases and reached two different dispositions.

Strongest SOC decisions:

- did not call Case 01 malicious simply because the rule fired;
- did not call the Case 02 Route 53 source a confirmed attacker endpoint;
- escalated Case 02 because ownership/authorization remained unresolved after a strong behavioral reconstruction.

## IR assessment

Lubaba independently reproduced the handoff, then expanded only where it answered real questions.

Strongest IR decisions:

- separated web correlation from DNS attribution;
- parsed raw VPC Flow data when extracted fields were unreliable;
- kept the incident within Reconnaissance because later stages were not proven;
- reviewed the actual public record exposure;
- rejected unsupported active containment.

## Detection Engineering feedback

No post-exercise evidence required changing the frozen v1.0 threshold.

Carry forward:

- keep `observed_dns_source` neutral;
- retain exact raw-event pivots;
- keep NXDOMAIN as supporting context;
- maintain asset/business enrichment for SOC;
- treat Web/VPC evidence as investigation context rather than mandatory DNS-detection conditions;
- re-baseline if traffic volume materially changes.

## Final scenario result

> **PASSED AS DESIGNED, WITH IMPORTANT ANALYST/IR LESSONS**

Scenario 01 demonstrated the complete chain:

```text
real behavior
→ target-side telemetry
→ evidence-based detection
→ AI assistance
→ human SOC investigation
→ evidence-backed IR validation
→ proportionate response decision
```

The final outcome was realistic because the exercise did not require every alert to be malicious and did not require every incident to end with a block.

---

<div align="center">

[🏠 Scenario Home](../README.md) · [📁 Exercise](README.md) · [⬆ Back to top](#top)

<sub>DNSentinel Lab · Evidence-first DNS security engineering</sub>

</div>
