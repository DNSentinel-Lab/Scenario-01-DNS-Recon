# Scenario 01 Runbook — DNS Reconnaissance & Enumeration

**Overall scenario status:** Official blind SOC/IR exercise pending  
**Detection Engineering status:** **✅ Complete / Detection Engineering Ready**  
**Primary MITRE ATT&CK:** `T1590.002 — Gather Victim Network Information: DNS`  
**Cyber Kill Chain:** Reconnaissance  
**Project Lead / Adversary Operator:** Abdul-Rehman  
**SOC Analyst / Threat Hunter:** Musfira  
**Detection Engineer:** Sonia  
**IR / Defender:** Lubaba

This runbook separates two things that must not be mixed:

1. **Detection Engineering validation** — already complete.
2. **Official blind adversary-vs-defender exercise** — pending execution.

> [!IMPORTANT]
> The official attacker operates from a separate AWS account, with optional external Windows traffic. The SOC Analyst is not told the attack start time, source IP, commands or queried names. Ground truth is revealed only after the SOC disposition is locked.

## Status summary

| # | Area | Current status |
|---:|---|---|
| 1 | Objective | ✅ Defined |
| 2 | Architecture | ✅ Blind external-adversary model defined |
| 3 | Prerequisites | ✅ Engineering gates passed; live health check pending |
| 4 | Adversary Exercise | ⏳ Playbook ready; official run pending |
| 5 | Telemetry | ✅ Validated |
| 6 | Detection | ✅ Complete |
| 7 | SPL / Detection Logic | ✅ Complete |
| 8 | Alert | ✅ Complete and validated |
| 9 | AI Triage | ✅ Engineering integration validated; live human comparison pending |
| 10 | SOC Analysis | 🟡 Playbook ready; official blind investigation pending |
| 11 | Incident Response | 🟡 Handoff boundary ready; official response pending |
| 12 | Evidence | ✅ Engineering evidence complete; live evidence pending |
| 13 | Containment | ⏳ Pending SOC/IR decision |
| 14 | Verification | ⏳ Pending approved response |
| 15 | Results | 🟡 Detection Engineering complete; final scenario result pending |
| 16 | MITRE ATT&CK Mapping | ✅ `T1590.002` |
| 17 | False Positives / Disposition | ✅ Decision model defined; live result pending |
| 18 | Lessons Learned | ✅ Detection Engineering lessons documented |
| 19 | Reproduction / Execution | ✅ Engineering + blind exercise paths documented |
| 20 | Screenshots / Evidence | ✅ Engineering set complete; live set pending |

---

## 1. Objective

Scenario 01 tests whether an external actor can enumerate the public DNS namespace and whether the defender can independently detect, investigate and explain that behavior.

The official exercise must answer two different questions:

```text
Adversary question:
What can I learn from public DNS?

Defender question:
Can I prove what happened from telemetry without being told the answer?
```

**Status:** ✅ Defined.

---

## 2. Architecture

### Official blind exercise path

```text
EXTERNAL ADVERSARY SIDE                         DEFENDER / SOC SIDE

Separate AWS account                           Defender AWS account
Kali attacker                                  Route 53 child zone
        \                                       dns-soc-web01
         \ public Internet                      dns-soc-splunk01
          +------ DNS / optional HTTPS --------> Splunk + AI bridge
                                                     |
                                                     v
                                                SOC Analyst
                                                     |
                                                     v
                                                  IR / Defender

Optional external Windows source
        +------ public Internet --------------------+
```

There is:

- no VPC peering;
- no private attacker-to-SOC route;
- no defender-side asset inventory identifying the official attacker;
- no attacker ground truth sent into the AI prompt.

The official adversary environment is outside the defender AWS account. Historical in-account attacker infrastructure used during early lab engineering is not the official blind-exercise source.

**Status:** ✅ Locked.

---

## 3. Prerequisites

### Defender health gate

Before the live window, Sonia reconfirms:

- current Route 53 events in `index=dns_soc_aws`;
- `sourcetype="aws:kinesis"` is current;
- Kinesis collection is healthy;
- Detection v1.0 is enabled;
- scheduled alert is enabled;
- dashboard loads;
- AI bridge health is green;
- `dns_soc_ai` can receive returned triage events.

Then **freeze Detection v1.0**. No threshold changes during the official run.

### SOC readiness gate

Musfira prepares:

- Alerts page;
- Scenario 01 dashboard;
- raw Route 53 search;
- SOC investigation template;
- AI triage search.

She is not told the attacker start time.

### Adversary readiness gate

Abdul-Rehman privately confirms:

- separate-account Kali host is reachable;
- public Internet/DNS works;
- current public source IP is recorded;
- UTC time is correct;
- the ground-truth template is ready.

**Status:** ✅ Design ready / live reconfirmation pending.

---

## 4. Adversary Exercise

### Threat objective

The adversary knows a public project domain and attempts to discover:

- authoritative nameservers;
- SOA/zone metadata;
- A/AAAA exposure;
- mail records;
- TXT metadata;
- likely public subdomains;
- whether a zone transfer is exposed;
- an optional public web endpoint discovered through DNS.

This behavior maps to:

**`T1590.002 — Gather Victim Network Information: DNS`**

and the **Reconnaissance** stage of the Cyber Kill Chain.

### Official attacker-side procedure

The full command sequence is in:

[`attacker/SCENARIO-01-ADVERSARY-PLAYBOOK.md`](attacker/SCENARIO-01-ADVERSARY-PLAYBOOK.md)

The attacker deliberately does **not** tune activity to Sonia's detection threshold.

Example activity includes:

```text
NS / SOA lookup
→ direct authoritative A / AAAA / MX / NS / TXT queries
→ small likely-subdomain enumeration
→ AXFR check
→ optional non-destructive HTTPS follow-up
```

Actual start time, public IP and commands remain private until the reveal gate.

**Status:** ⏳ Official run pending.

---

## 5. Telemetry

### Primary evidence source

```text
index=dns_soc_aws
sourcetype=aws:kinesis
```

Validated Route 53 authoritative fields:

```text
query_name
query_type
response_code
protocol
edge_location
observed_dns_source
edns_client_subnet
```

`observed_dns_source` is intentionally neutral. It is the source/resolver address seen by Route 53, not automatic proof of the original endpoint or human attacker.

### Supporting evidence

| Source | Scenario 01 use |
|---|---|
| Nginx access logs | Optional public-web follow-up |
| VPC Flow Logs | Network follow-up to the Web EC2 |
| CloudTrail | Defender/cloud configuration context if relevant |
| Resolver Query Logs | Not a primary official Scenario 01 source; more important from Scenario 02 onward |
| `dns_soc_ai` | AI-assisted context; never the primary evidence |

### Network / protocol interpretation

| Layer/view | Scenario 01 evidence |
|---|---|
| Layer 7 | DNS names, record types and responses at Route 53 authority |
| Layer 4 | UDP/TCP 53; optional HTTP/HTTPS follow-up on 80/443 |
| Layer 3 | source/destination IP context from target-side logs where available |
| Application | Nginx request details for optional public-Web follow-up |

**Status:** ✅ Validated.

---

## 6. Detection

Final behavioral hypothesis:

```text
same observed DNS source
+ controlled lab namespace
+ concentrated 1-minute activity
+ query-name breadth
+ meaningful record-type diversity
→ possible DNS reconnaissance
```

Detection v1.0 threshold:

```text
query_count >= 16
unique_names >= 4
distinct_query_types >= 3
```

`NXDOMAIN` remains supporting context, not a required condition.

The official adversary is not instructed to cross these values. If the real behavior bypasses the rule, that is a valid detection-gap result.

**Status:** ✅ Complete / frozen for live exercise.

---

## 7. SPL / Detection Logic

Implemented and tested:

- [`spl/baseline.spl`](spl/baseline.spl)
- [`spl/hunting.spl`](spl/hunting.spl)
- [`spl/detection.spl`](spl/detection.spl)
- [`spl/validation.spl`](spl/validation.spl)

Thresholds came from measured baseline behavior and controlled positive/benign testing.

**Status:** ✅ Complete.

---

## 8. Alert

```text
Name:            Scenario 01 - Possible DNS Reconnaissance
Schedule:        * * * * *
Lookback:        Last 3 minutes
Trigger:         Number of Results > 0
Trigger mode:    Once
Throttle:        60 seconds
Severity:        Medium
Actions:         Triggered Alerts + Webhook
```

The alert is a **lead**, not a final incident verdict.

See [`spl/scheduled-alert.md`](spl/scheduled-alert.md).

**Status:** ✅ Complete and validated.

---

## 9. AI Triage

The final alert sends:

```text
alert_id
alert_name
scenario
severity
event_time
source
evidence_json
```

Engineering validation proved:

```text
scheduled alert
→ Splunk native webhook
→ AI bridge
→ OpenAI HTTP 200
→ internal HEC
→ index=dns_soc_ai
```

The live analyst must validate AI against raw evidence.

AI may:

- summarize the behavior;
- list indicators;
- suggest suspicion reasons;
- suggest MITRE/Kill Chain context;
- identify missing evidence;
- suggest response considerations.

AI may **not**:

- prove attacker identity;
- replace raw logs;
- decide TP/FP automatically;
- authorize containment.

`human_validation_required=true` remains mandatory.

**Status:** ✅ Engineering path validated / live comparison pending.

---

## 10. SOC Analysis

The official analyst workflow is documented in:

[`soc/SOC-ANALYST-PLAYBOOK.md`](soc/SOC-ANALYST-PLAYBOOK.md)

Musfira works from defender evidence only:

```text
Alert
→ Dashboard
→ Raw Route 53 events
→ Historical source behavior
→ Optional Web/VPC correlation
→ AI validation
→ 5W1H investigation
→ disposition
→ IR handoff if warranted
```

### Blindness rule

The SOC Analyst does not receive attacker ground truth before the decision.

### Disposition model

| Disposition | Meaning |
|---|---|
| **TP — suspicious/unauthorized** | Real reconnaissance behavior with no established legitimate explanation |
| **TP — authorized/benign** | Real reconnaissance behavior, later verified as approved activity |
| **FP** | Normal/non-recon behavior incorrectly matched the detection |
| **Inconclusive** | Evidence is insufficient for a safe conclusion |

This separates **detection correctness** from **business/security context**.

**Status:** 🟡 Playbook ready / official investigation pending.

---

## 11. Incident Response

IR receives the SOC evidence package only after Musfira has investigated the alert.

The handoff should contain:

- alert ID;
- UTC first/last seen;
- observed source;
- queried names/types;
- response/NXDOMAIN context;
- supporting web/network evidence;
- MITRE + Kill Chain context;
- AI-vs-human assessment;
- SOC disposition and confidence;
- unknowns requiring IR authority/context.

No AI output or Detection Engineering test automatically authorizes containment.

**Status:** 🟡 Handoff model ready / official response pending.

---

## 12. Evidence

### Existing engineering evidence

- [`DETECTION-ENGINEERING.md`](DETECTION-ENGINEERING.md)
- [`evidence/detection-engineering-validation.md`](evidence/detection-engineering-validation.md)
- [`screenshots/`](screenshots/)

### Live official exercise evidence to add

```text
private attacker ground truth
SOC 5W1H notes
alert results
raw Route 53 events
dashboard evidence
AI triage + human validation
IR handoff / action
post-response verification
final attacker-vs-defender comparison
```

The completed attacker ground truth must remain private until the SOC disposition is locked.

**Status:** ✅ Engineering evidence complete / live evidence pending.

---

## 13. Containment

Scenario 01 is reconnaissance. A response decision must be proportionate to what was actually observed.

Possible IR decisions may include:

- preserve and monitor only;
- restrict an identified source where technically appropriate;
- reduce unnecessary public DNS exposure;
- review public records that reveal unnecessary metadata;
- apply a network/web control if follow-up behavior justifies it.

The exact action is selected by IR after SOC confirmation. It is not pre-scripted into the alert.

**Status:** ⏳ Pending live decision.

---

## 14. Verification

If IR changes anything, verify before/after evidence.

Examples:

```text
Did the source stop reaching the affected public service?
Did unnecessary DNS exposure actually change?
Did expected legitimate access remain available?
Did Splunk record the post-response state?
```

A response without verification is incomplete.

**Status:** ⏳ Pending response.

---

## 15. Results

### Detection Engineering

**PASS — complete.**

The platform has a validated baseline, dashboard, hunting SPL, detection v1.0, scheduled alert, raw-event pivot and AI evidence path.

### Official blind exercise

Not yet assigned.

A valid result can be:

- alert + correct SOC disposition;
- alert + useful tuning lesson;
- detection miss found through later ground-truth comparison;
- AI disagreement correctly caught by the analyst;
- insufficient evidence correctly marked inconclusive.

**Status:** 🟡 Final scenario result pending.

---

## 16. MITRE ATT&CK Mapping

Primary mapping:

**`T1590.002 — Gather Victim Network Information: DNS`**

Why:

- nameserver/zone metadata is gathered;
- DNS records are enumerated;
- likely public names are tested;
- mail/TXT/address exposure may be collected;
- AXFR exposure may be checked.

Cyber Kill Chain:

**Reconnaissance**

No additional technique is added simply because an alert or AI model suggests one. Additional mapping requires evidence of additional behavior.

**Status:** ✅ Defined.

---

## 17. False Positives / Disposition

Detection Engineering already proved that record-type diversity alone is too noisy.

During the official exercise, the analyst must distinguish four cases:

```text
real recon + unauthorized context     → TP suspicious
real recon + proven approved context  → TP behavior / benign context
normal DNS matched as recon           → FP
insufficient evidence                 → inconclusive
```

The analyst must not label something FP merely because attacker intent cannot yet be proven.

**Status:** ✅ Decision model ready / live classification pending.

---

## 18. Lessons Learned

Existing engineering lessons are in [`DETECTION-ENGINEERING.md`](DETECTION-ENGINEERING.md).

The blind-exercise design adds several operational lessons:

- defender evidence must stand on its own;
- attacker ground truth should not contaminate SOC reasoning;
- detection and business-context decisions are different;
- negative correlation is not proof when DNS resolver intermediaries exist;
- AI must be challenged, not accepted;
- a detection miss is valid evidence and must not be hidden by live tuning.

**Status:** ✅ Prepared for post-exercise update.

---

## 19. Reproduction / Execution Instructions

### Detection Engineering reproduction

```text
1. Confirm Route 53 events
2. Map fields
3. Measure ingestion
4. Run baseline.spl
5. Review dashboard
6. Run hunting.spl
7. Test detection.spl
8. Run controlled positive and benign engineering validation
9. Validate scheduled alert
10. Validate AI evidence contract
```

### Official blind exercise

```text
1. Reconfirm defender platform health
2. Freeze Detection v1.0
3. SOC begins monitoring without attack timing
4. External attacker records source IP/time privately
5. Attacker performs DNS reconnaissance from separate AWS account
6. Alert may fire — or may miss
7. SOC independently investigates
8. SOC validates AI against raw evidence
9. SOC locks TP / authorized TP / FP / inconclusive disposition
10. SOC hands off to IR when warranted
11. IR decides/executes/verifies any response
12. Project Lead reveals attacker ground truth
13. Team compares attacker vs telemetry vs detection vs AI vs SOC vs IR
14. Detection changes happen only after this comparison
```

See [`exercise/BLIND-EXERCISE-PROTOCOL.md`](exercise/BLIND-EXERCISE-PROTOCOL.md).

**Status:** ✅ Documented / execution pending.

---

## 20. Screenshots / Evidence

Current Detection Engineering evidence is indexed in [`screenshots/README.md`](screenshots/README.md).

The official exercise should add only evidence that proves decisions:

- alert trigger;
- raw DNS events;
- dashboard source/window;
- AI output;
- SOC notes/disposition;
- IR action/verification;
- attacker ground-truth proof after reveal.

Do not publish repetitive progress screenshots or attacker ground truth before the reveal gate.

**Status:** ✅ Engineering set complete / live set pending.

---

# Official exercise handoff

The project is now ready for this realistic operating chain:

```text
External adversary acts without notifying SOC
        ↓
Route 53 records what actually reached authority
        ↓
Detection v1.0 raises a lead if the behavior crosses the rule
        ↓
AI produces advisory context
        ↓
SOC Analyst independently proves/rejects the hypothesis
        ↓
SOC locks disposition and confidence
        ↓
IR receives the evidence-backed handoff
        ↓
Ground truth is revealed afterward
        ↓
Team measures detection + AI + analyst accuracy
```

That separation is the core of the official Scenario 01 exercise.
