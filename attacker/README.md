# Attacker / Adversary Workspace — Scenario 01

**Role owner:** Abdul-Rehman — Project Lead / Adversary Operator  
**Official exercise model:** blind external adversary exercise  
**Primary MITRE ATT&CK:** `T1590.002 — Gather Victim Network Information: DNS`  
**Cyber Kill Chain stage:** Reconnaissance

This folder contains the attacker-side operating plan for the official Scenario 01 exercise.

The attacker and defender are intentionally separated. The official Kali host runs from a **different AWS account** from the defender/SOC platform. An external Windows machine may also be used as a second public source. There is no private route, peering relationship or defender-side asset context that identifies these systems to the SOC Analyst.

## Files

- [`SCENARIO-01-ADVERSARY-PLAYBOOK.md`](SCENARIO-01-ADVERSARY-PLAYBOOK.md) — attacker objective, sequence and commands.
- [`ground-truth-template.md`](ground-truth-template.md) — private execution record retained until the SOC decision is locked.

## Information-separation rule

During the live exercise, the attacker does **not** disclose:

- start time;
- public source IP;
- queried hostnames;
- commands;
- whether an alert fired;
- the intended attacker conclusion.

The SOC Analyst must work only from defender-visible evidence.

Actual ground truth is revealed **after** the analyst has recorded a disposition and IR has received the handoff.

## Scope boundary

Scenario 01 is reconnaissance only. The adversary may generate real DNS reconnaissance traffic against the project-owned public namespace and perform a small non-destructive web follow-up if desired. The exercise does not include exploitation, credential attacks, destructive actions, denial of service or unrelated Internet targets.
