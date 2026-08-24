<a id="top"></a>

> 🧭 [Scenario 01](../README.md) › [Soc](README.md) › **Scenario 01 SOC Analyst Playbook — DNS Reconnaissance & Enumeration**

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=flat-square)
![DNSentinel](https://img.shields.io/badge/DNSentinel-Technical_Record-00F5FF?style=flat-square)

---

# Scenario 01 SOC Analyst Playbook — DNS Reconnaissance & Enumeration

**Analyst:** [Musfira](https://github.com/MUSFIRA-ZAFAR)  
**Status:** ✅ Workflow used for the completed Scenario 01 SOC investigations  
**Role:** SOC Analyst / Threat Hunter  
**Primary MITRE ATT&CK:** `T1590.002 — Gather Victim Network Information: DNS`  
**Cyber Kill Chain:** Reconnaissance  
**Detection:** `Scenario 01 - Possible DNS Reconnaissance`

## 1. What the analyst knows — and does not know

The analyst knows:

- the organization owns `soclab.abdul4rehman215.tech`;
- Route 53 authoritative telemetry is in Splunk;
- Detection v1.0 is enabled;
- the investigation dashboard and AI assistance are available.

The analyst does **not** know:

- whether an attack is happening;
- when it starts;
- which external system is involved;
- the attacker's IP;
- what commands were run;
- which names will be queried.

Treat the queue like a real SOC investigation: do not use attacker-side ground truth to reach the conclusion.

---

## Network and protocol view

| View | What the analyst should read |
|---|---|
| **Layer 7 — DNS** | queried name, record type, response code, NXDOMAIN pattern, authoritative behavior |
| **Layer 4** | DNS over UDP/TCP 53; optional follow-up HTTP/HTTPS on 80/443 |
| **Layer 3** | public source/destination identities where target-side logs expose them |
| **Application** | Nginx method/URI/status if a discovered web host is visited |
| **Cloud/SIEM** | Route 53 log delivery, Splunk alert, AI event and timeline |

Scenario 01 is primarily a **DNS application-layer reconnaissance investigation**. Network-layer evidence supports the story but should not replace the authoritative DNS logs.

---

# LIVE WORKFLOW

## Step 0 — Start the SOC shift

1. Open **Splunk Enterprise**.
2. Go to **Search & Reporting**.
3. Confirm the current time and use UTC in notes.
4. Open **Alerts** and confirm `Scenario 01 - Possible DNS Reconnaissance` is enabled.
5. Open the **Scenario 01 — DNS Reconnaissance Investigation** dashboard in another tab.
6. Do not ask the Project Lead whether the attack has started.

If the alert does not fire, continue monitoring and use the hunting section later in this playbook.

---

## Step 1 — Triage the alert

When an alert appears:

1. In Splunk, click **Alerts**.
2. Click **Scenario 01 - Possible DNS Reconnaissance**.
3. Under **Trigger History**, open the newest trigger.
4. Click **View Results**.
5. Record these fields immediately:

```text
alert_id
first_event_display
last_event_display
observed_dns_source
query_count
unique_names
distinct_query_types
query_types
query_name_samples
response_codes
nxdomain_count
severity
mitre_technique
```

### First question

**Does the result actually match the rule?**

Expected v1.0 boundary:

```text
query_count >= 16
unique_names >= 4
distinct_query_types >= 3
```

If the row does not meet those conditions, treat it as a detection/configuration issue and notify the Detection Engineer.

If it does, continue investigation. Do not call it malicious yet.

---

## Step 2 — Use the investigation dashboard

1. Open **Dashboards**.
2. Open **Scenario 01 — DNS Reconnaissance Investigation**.
3. Set **Global Time Range** to cover about 10 minutes before and after the alert.
4. Set **Observed DNS Source** to the alert's `observed_dns_source`.
5. Read the dashboard in this order:

```text
Total DNS Queries
→ Unique Queried Names
→ Distinct Query Types
→ DNS Activity Over Time
→ Record-Type Diversity Over Time
→ Top Queried Names
→ Response Distribution
→ Top 1-Minute DNS Bursts
→ DNS Investigation Events
```

Look for a **pattern**, not one event.

Questions:

- Was activity compressed into a short time?
- Were several different names tested?
- Were A, AAAA, MX, NS, TXT or similar record types queried?
- Were nonexistent names tried?
- Does the activity look systematic?

---

## Step 3 — Prove the alert with raw Route 53 events

Go to **Search & Reporting → Search**.

Set the time picker around the alert window and run:

```spl
index=dns_soc_aws sourcetype="aws:kinesis"
| rex field=_raw "^(?<log_version>\S+)\s+(?<route53_event_time>\S+)\s+(?<hosted_zone_id>\S+)\s+(?<query_name>\S+)\s+(?<query_type>\S+)\s+(?<response_code>\S+)\s+(?<protocol>\S+)\s+(?<edge_location>\S+)\s+(?<observed_dns_source>\S+)\s+(?<edns_client_subnet>\S+)"
| eval query_name=lower(rtrim(query_name,"."))
| where query_name="soclab.abdul4rehman215.tech" OR match(query_name,"\.soclab\.abdul4rehman215\.tech$")
| table _time observed_dns_source query_name query_type response_code protocol edge_location edns_client_subnet
| sort _time
```

Then narrow to the alert source by adding:

```spl
| where observed_dns_source="<SOURCE_FROM_ALERT>"
```

### What to verify

- first and last observed event;
- exact queried names;
- record-type sequence;
- `NOERROR` vs `NXDOMAIN`;
- UDP/TCP protocol;
- whether the source stayed consistent.

> `observed_dns_source` is what Route 53 saw. It is not automatically the human attacker or original endpoint.

---

## Step 4 — Compare with historical behavior

The most useful question is:

**Is this source/window normal for this namespace?**

Set a longer historical range such as **Last 24 hours** or **Last 7 days** and run:

```spl
index=dns_soc_aws sourcetype="aws:kinesis"
| rex field=_raw "^(?<log_version>\S+)\s+(?<route53_event_time>\S+)\s+(?<hosted_zone_id>\S+)\s+(?<query_name>\S+)\s+(?<query_type>\S+)\s+(?<response_code>\S+)\s+(?<protocol>\S+)\s+(?<edge_location>\S+)\s+(?<observed_dns_source>\S+)\s+(?<edns_client_subnet>\S+)"
| eval query_name=lower(rtrim(query_name,"."))
| where observed_dns_source="<SOURCE_FROM_ALERT>"
| where query_name="soclab.abdul4rehman215.tech" OR match(query_name,"\.soclab\.abdul4rehman215\.tech$")
| bin _time span=1m
| stats count as query_count
        dc(query_name) as unique_names
        dc(query_type) as distinct_query_types
        values(query_type) as query_types
        values(query_name) as query_names
        values(response_code) as response_codes
  by _time observed_dns_source
| sort - _time
```

Look for:

- repeated similar bursts;
- one isolated unusual burst;
- normal low-volume activity;
- a source that repeatedly behaves like a resolver/crawler rather than a single endpoint.

Historical repetition does not automatically mean benign. It is context.

---

## Step 5 — Check for public web follow-up

DNS reconnaissance becomes more interesting if the public source later visits the discovered web target.

Search Nginx around the same time:

```spl
index=dns_soc_web sourcetype="dns_soc:nginx:access"
| rex field=_raw "^(?<client_ip>\S+)\s+\S+\s+\S+\s+\[(?<web_time>[^\]]+)\]\s+\"(?<method>\S+)\s+(?<uri>\S+)\s+(?<http_version>[^\"]+)\"\s+(?<status>\d{3})\s+(?<bytes>\S+)"
| table _time client_ip method uri status bytes host source
| sort _time
```

Then investigate any suspicious public source around the DNS window.

### Important correlation rule

If `client_ip` does **not** equal `observed_dns_source`, do not conclude there was no follow-up. Public DNS may have been resolved through an intermediary resolver.

A positive match is useful supporting evidence. A negative match is not proof of innocence.

---

## Step 6 — Check VPC Flow Logs for follow-up traffic

Use this only for traffic that reached the defender VPC/web host. Route 53 authoritative DNS itself is outside the web EC2 flow-log path.

```spl
index=dns_soc_aws sourcetype="aws:cloudwatchlogs:vpcflow"
| eval src_ip=coalesce(src_ip,src,srcaddr)
| eval dest_ip=coalesce(dest_ip,dest,dst,dstaddr)
| eval dport=coalesce(dest_port,dst_port,dstport)
| search dest_ip="10.50.10.10" (dport=80 OR dport=443)
| table _time src_ip dest_ip dport action bytes packets
| sort _time
```

Look for inbound HTTP/HTTPS activity near the DNS reconnaissance window.

Do not force a source-IP correlation when the DNS source may be a resolver.

---

## Step 7 — Read the AI result, then challenge it

Go to **Search & Reporting** and run:

```spl
index=dns_soc_ai
| spath path=alert.alert_id output=alert_id
| spath path=alert.alert_name output=alert_name
| spath path=alert.scenario output=scenario
| spath path=alert.severity output=severity
| spath path=ai.summary output=summary
| spath path=ai.confidence output=confidence
| spath path=ai.observed_indicators{} output=observed_indicators
| spath path=ai.suspicion_reasons{} output=suspicion_reasons
| spath path=ai.mitre_attack.tactic output=mitre_tactic
| spath path=ai.mitre_attack.technique_id output=mitre_technique_id
| spath path=ai.mitre_attack.technique_name output=mitre_technique_name
| spath path=ai.cyber_kill_chain.stage output=kill_chain_stage
| spath path=ai.missing_evidence{} output=missing_evidence
| spath path=ai.response_considerations{} output=response_considerations
| eval observed_indicators=mvjoin(observed_indicators," | ")
| eval suspicion_reasons=mvjoin(suspicion_reasons," | ")
| eval missing_evidence=mvjoin(missing_evidence," | ")
| eval response_considerations=mvjoin(response_considerations," | ")
| eval mitre_tactic=mvindex(mitre_tactic,0)
| eval mitre_technique_id=mvindex(mitre_technique_id,0)
| eval mitre_technique_name=mvindex(mitre_technique_name,0)
| eval kill_chain_stage=mvindex(kill_chain_stage,0)
| sort - _time
| table _time alert_id alert_name scenario severity summary confidence observed_indicators suspicion_reasons mitre_tactic mitre_technique_id mitre_technique_name kill_chain_stage missing_evidence response_considerations human_validation_required processed_at request_id
```

Validate the AI in four areas:

| Check | Analyst question |
|---|---|
| Facts | Did AI correctly read count, source, names, types and time? |
| Reasoning | Does the raw evidence support the explanation? |
| Framework | Is `T1590.002` / Reconnaissance a better fit than another technique? |
| Limits | Did AI claim identity, intent or impact that the logs do not prove? |

Record one of:

```text
AI assessment: Correct
AI assessment: Partially correct
AI assessment: Incorrect
```

AI does not decide TP/FP and does not authorize response.

---

## Step 8 — Build the 5W1H story

Before deciding, write one short answer for each:

| Question | Scenario 01 example |
|---|---|
| **Who?** | Which source/resolver identity was observed? Any known owner? |
| **What?** | What DNS enumeration behavior occurred? |
| **When?** | First seen, last seen, alert time, follow-up time |
| **Where?** | Public Route 53 child zone; any web/VPC follow-up |
| **Why suspicious?** | What pattern is abnormal compared with baseline? |
| **How?** | Which names/types/frequency/response pattern produced the conclusion? |

Stay factual. If identity or intent is unknown, write **unknown**.

---

# DISPOSITION

## Step 9 — Decide TP, benign/authorized, FP or inconclusive

Use **two questions**, not one.

### Question A — Did the reconnaissance behavior really occur?

Evidence for a true behavioral detection:

- concentrated burst;
- several queried names;
- several DNS record types;
- systematic enumeration sequence;
- behavior materially different from baseline.

### Question B — Is there a legitimate explanation?

Possible legitimate context may include:

- approved administration;
- known monitoring/scanning service;
- expected DNS research;
- verified organizational testing;
- resolver behavior that makes the pattern look broader than one endpoint.

Do not invent authorization. It must be supported by available business/asset context or later confirmation through the proper escalation path.

### Recommended disposition model

| Disposition | Meaning | Action |
|---|---|---|
| **TP — suspicious/unauthorized** | The reconnaissance behavior is real and no legitimate explanation is established | Escalate to IR |
| **TP — authorized/benign** | The behavior is real, but verified context shows it is approved | Close with evidence; detection still worked |
| **FP** | Normal/non-recon behavior incorrectly satisfied the rule | Close and send tuning feedback to Detection Engineering |
| **Inconclusive** | Evidence is insufficient for a safe verdict | Continue investigation or escalate for additional context |

### Key rule

A technically correct alert can still represent benign authorized activity. That is **not** the same as a broken detection.

---

## Step 10 — Escalate to IR

Escalate when:

- the pattern is a true reconnaissance detection;
- the activity is unexplained or unauthorized;
- the source is external/untrusted or unattributed;
- supporting evidence increases concern;
- more authority is needed to validate ownership or apply containment.

Send IR a concise handoff:

```text
Incident candidate: DNS Reconnaissance & Enumeration
Alert ID:
First seen UTC:
Last seen UTC:
Observed DNS source:
Query count:
Unique names:
Distinct query types:
Query types:
Response codes / NXDOMAIN context:
Supporting Nginx evidence:
Supporting VPC Flow evidence:
MITRE: T1590.002
Cyber Kill Chain: Reconnaissance
AI assessment: Correct / Partially correct / Incorrect
SOC disposition:
Why escalated:
What is still unknown:
Evidence links / screenshots:
```

Do not call the observed DNS source "the attacker" unless attribution has actually been proven.

---

# IF NO ALERT FIRES

A realistic exercise can also produce a detection miss.

During the monitoring window, use the first block from [`../spl/hunting.spl`](../spl/hunting.spl) to look for unusual one-minute source/windows.

If hunting finds reconnaissance but the alert did not fire:

```text
SOC finding = suspicious behavior found by hunt
Detection outcome = miss / detection gap
Action = escalate evidence + send Detection Engineering feedback after exercise
```

Do not change the live rule during the exercise.

---

# POST-INVESTIGATION GATE

## Step 11 — Lock the SOC decision before ground-truth reveal

Before anyone reveals attacker information, save:

- SOC disposition;
- confidence level;
- 5W1H notes;
- AI validation result;
- raw evidence screenshots/links;
- IR handoff if escalated.

Only then may the Project Lead reveal attacker ground truth.

## Step 12 — Compare with ground truth

After reveal, compare four views:

```text
What the attacker actually did
        VS
What Route 53 / Splunk observed
        VS
What Detection v1.0 alerted on
        VS
What AI and the SOC Analyst concluded
```

Record:

- what the SOC got right;
- what was missing;
- any attribution limitation;
- any detection miss or noisy field;
- AI mistakes;
- whether a detection change is justified **after** the exercise.

---

# FAST DECISION CARD

```text
ALERT
  |
  v
Does raw Route 53 evidence support the alert?
  |-- No --> FP / detection issue
  |
 Yes
  v
Does behavior look systematic and abnormal vs baseline?
  |-- No --> likely FP / benign DNS pattern
  |
 Yes
  v
Is legitimate/authorized context proven?
  |-- Yes --> TP behavior, benign/authorized context
  |
 No / Unknown
  v
Check follow-up + AI + source history
  |
  v
TP suspicious OR Inconclusive
  |
  v
IR handoff when warranted
```

## Analyst principle

**Logs are evidence, not answers. The alert starts the investigation; the analyst finishes it.**

---

<div align="center">

[🏠 Scenario Home](../README.md) · [📁 Soc](README.md) · [⬆ Back to top](#top)

<sub>DNSentinel Lab · Evidence-first DNS security engineering</sub>

</div>
