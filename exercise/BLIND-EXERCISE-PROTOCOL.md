# Scenario 01 Blind Exercise Protocol

## Purpose

Run Scenario 01 as a realistic adversary-vs-defender exercise while preserving the existing lab safety boundary and the integrity of the SOC investigation.

The attacker uses real public DNS requests against the project-owned namespace. The SOC Analyst sees only defender-side telemetry and must independently decide what happened.

## Architecture

```text
EXTERNAL ADVERSARY SIDE                         DEFENDER / SOC SIDE

Separate AWS account                           Defender AWS account
Kali attacker                                  Route 53 authoritative DNS
        \                                       dns-soc-web01
         \                                      dns-soc-splunk01
          \ public Internet only                Splunk + AI bridge
           \                                         |
            +---------- DNS / HTTPS -----------------+
                                                      |
                                              SOC Analyst / IR

Optional external Windows machine
        \------------ public Internet ---------------+
```

There is no attacker-to-defender private route and no defender-side asset inventory that identifies the official attacker machine.

## Role boundaries

| Role | Live responsibility | Information held back |
|---|---|---|
| **Project Lead / Adversary — Abdul-Rehman** | Execute external DNS reconnaissance and preserve ground truth | Start time, public IP, commands, queried names |
| **SOC Analyst — Musfira** | Monitor, investigate, validate AI, classify and hand off | Receives no attacker ground truth before disposition |
| **Detection Engineer — Sonia** | Keep v1.0 healthy; provide platform support only | Does not tune thresholds during the run |
| **IR / Defender — Lubaba** | Receive SOC handoff and make response decision | Does not receive attacker ground truth before SOC handoff |

## Non-negotiable realism rules

1. **No attack countdown to the SOC Analyst.**
2. **No attacker IP shared before investigation.**
3. **No attacker commands shared before investigation.**
4. **No mid-exercise detection tuning.**
5. **AI receives only alert evidence, not attacker ground truth.**
6. **SOC disposition is written before ground truth is revealed.**
7. **If the alert misses the activity, the miss is preserved as evidence instead of fixing the rule live.**

## Exercise preparation

### Detection Engineer — before the live window

Confirm only platform health:

```text
Route 53 telemetry current
Kinesis input healthy
Detection v1.0 enabled
Scheduled alert enabled
Dashboard loads
AI bridge healthy
HEC result path healthy
```

Then freeze the detection.

### SOC Analyst — before the live window

Prepare:

- Splunk Alerts page;
- Scenario 01 dashboard;
- raw Route 53 hunt;
- investigation notes/template;
- AI triage search.

The analyst may know the overall exercise day/window, but not the exact attack start.

### IR — before the live window

Prepare the response checklist and evidence handoff path. Do not pre-classify the event.

### Adversary — before the live window

Use the separate AWS account Kali host or optional external Windows source. Record source IP and UTC time privately.

## Live execution

### Phase 1 — monitoring begins

SOC starts normal monitoring.

No one announces "attack starting now."

### Phase 2 — adversary operates

The Project Lead follows [`../attacker/SCENARIO-01-ADVERSARY-PLAYBOOK.md`](../attacker/SCENARIO-01-ADVERSARY-PLAYBOOK.md) at an undisclosed time.

The attacker objective is DNS reconnaissance, not threshold matching.

### Phase 3 — detection outcome

One of two things happens:

```text
A. Alert fires
   -> SOC starts triage from the alert

B. Alert does not fire
   -> SOC continues shift monitoring/hunting
   -> detection miss is reviewed only after ground truth reveal
```

Both are valid exercise outcomes.

### Phase 4 — SOC investigation

Musfira follows [`../soc/SOC-ANALYST-PLAYBOOK.md`](../soc/SOC-ANALYST-PLAYBOOK.md).

She records:

- raw DNS evidence;
- timeline;
- source semantics;
- normal-vs-abnormal comparison;
- supporting web/network context;
- AI validation;
- TP / authorized TP / FP / inconclusive disposition.

### Phase 5 — IR handoff

If the SOC conclusion warrants escalation, Musfira sends the evidence package to Lubaba.

IR acts only on the defender evidence and approved response condition.

### Phase 6 — lock the defender record

Before reveal, record:

```text
SOC disposition
SOC confidence
AI validation
IR decision/action if applicable
screenshots / searches / timestamps
```

### Phase 7 — ground-truth reveal

Only now does Abdul-Rehman reveal:

- actual attacker public IP(s);
- actual UTC start/end;
- commands/actions;
- names/types queried;
- optional web follow-up;
- attacker-side outputs.

### Phase 8 — final comparison

Compare:

```text
Attacker ground truth
        ↓
Telemetry visibility
        ↓
Detection result
        ↓
AI result
        ↓
SOC decision
        ↓
IR decision / verification
```

Use [`final-comparison-template.md`](final-comparison-template.md).

## Decision quality measures

The team should score the exercise on questions such as:

- Did Route 53 capture the real DNS behavior?
- Did Detection v1.0 alert at the right time?
- If not, could SOC hunting still find the behavior?
- Did the analyst reconstruct the attacker behavior from evidence?
- Did the analyst avoid calling `observed_dns_source` a confirmed attacker without proof?
- Did AI summarize facts correctly?
- Did AI add unsupported assumptions?
- Was the SOC disposition correct against ground truth?
- Was escalation proportionate?
- Did IR preserve evidence and verify any response?

## Documentation rule

During the live run, attacker ground truth stays private. After the reveal, the completed ground-truth record may be added to the repository together with SOC/IR evidence and the final result.
