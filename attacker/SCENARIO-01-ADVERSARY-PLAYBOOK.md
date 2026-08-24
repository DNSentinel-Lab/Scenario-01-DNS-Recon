<a id="top"></a>

> 🧭 [Scenario 01](../README.md) › [Attacker](README.md) › **Scenario 01 Adversary Playbook — DNS Reconnaissance & Enumeration**

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=flat-square)
![DNSentinel](https://img.shields.io/badge/DNSentinel-Technical_Record-00F5FF?style=flat-square)

---

# Scenario 01 Adversary Playbook — DNS Reconnaissance & Enumeration

**Operator:** [Abdul-Rehman](https://github.com/abdul4rehman215)  
**Status:** ✅ Used for the completed Scenario 01 execution  
**Perspective:** external adversary  
**Target namespace:** `soclab.abdul4rehman215.tech`  
**MITRE ATT&CK:** `T1590.002 — Gather Victim Network Information: DNS`  
**Cyber Kill Chain:** Reconnaissance

## 1. Adversary objective

Act as an external party who knows only a public project domain and wants to learn what the DNS namespace reveals before attempting any later-stage activity.

The attacker is trying to answer:

```text
Who is authoritative for the domain?
What public names exist?
Which address records are exposed?
Is mail infrastructure exposed?
What TXT metadata is visible?
Do IPv6 records exist?
Can the zone be transferred?
Which discovered host could be checked next over HTTPS?
```

The attacker is **not** trying to satisfy the Splunk threshold. Follow the reconnaissance objective naturally and let the detection prove whether it can identify the behavior.

## 2. Exercise isolation

Scenario 01 attacker sources used by the exercise model:

```text
Separate AWS account
    └── Kali attacker EC2

Optional second source
    └── external Windows machine
```

Rules:

- no VPC peering or private route to the defender account;
- use only public DNS/public web paths;
- do not access the defender AWS account during the exercise;
- do not tell the SOC Analyst when the activity starts;
- keep actual commands and timestamps private until ground-truth reveal.

## 3. Pre-run attacker check

Run privately on Kali:

```bash
export DOMAIN="soclab.abdul4rehman215.tech"
date -u
curl -s https://checkip.amazonaws.com

dig +short NS "$DOMAIN"
dig +short A "$DOMAIN"
```

Record the UTC time and public source IP in the private ground-truth record.

If the public IP is different from a previously used source, do **not** tell the SOC Analyst.

## 4. Reconnaissance sequence — Kali/Linux

### Step A — identify authority and zone metadata

```bash
DOMAIN="soclab.abdul4rehman215.tech"

dig "$DOMAIN" NS
dig "$DOMAIN" SOA
```

What the attacker learns:

- authoritative Route 53 nameservers;
- SOA/zone metadata;
- confirmation that the namespace is publicly delegated.

### Step B — query the authoritative nameserver directly

```bash
AUTH_NS=$(dig +short NS "$DOMAIN" | head -n1)

dig @"$AUTH_NS" "$DOMAIN" A +norecurse
dig @"$AUTH_NS" "$DOMAIN" AAAA +norecurse
dig @"$AUTH_NS" "$DOMAIN" MX +norecurse
dig @"$AUTH_NS" "$DOMAIN" NS +norecurse
dig @"$AUTH_NS" "$DOMAIN" TXT +norecurse
```

This produces real authoritative DNS requests over the public Internet.

### Step C — enumerate likely public names

Use a small, deliberate list rather than an Internet-scale scanner:

```bash
for h in www api mail vpn dev stage; do
  dig @"$AUTH_NS" "$h.$DOMAIN" A +norecurse
  dig @"$AUTH_NS" "$h.$DOMAIN" AAAA +norecurse
done
```

Possible results:

- `NOERROR` for names that exist;
- `NXDOMAIN` for names that do not exist;
- different DNS record exposure across names.

Do not create fake Route 53 records just to help the attacker.

### Step D — test for an accidental zone transfer

```bash
dig @"$AUTH_NS" "$DOMAIN" AXFR +time=3 +tries=1
```

A refused/failed transfer is still useful attacker knowledge: the control is not exposed.

### Step E — optional public web follow-up

If DNS reveals a public web hostname, a small non-destructive request may be made:

```bash
curl -I "https://$DOMAIN/"
curl -I "https://www.$DOMAIN/"
```

This is optional supporting behavior. Scenario 01 remains a DNS reconnaissance scenario; do not turn it into port scanning or exploitation.

## 5. Optional Windows source

A second public source can make the defender investigation more realistic.

PowerShell examples:

```powershell
$Domain = "soclab.abdul4rehman215.tech"

Resolve-DnsName $Domain -Type NS
Resolve-DnsName $Domain -Type SOA
Resolve-DnsName $Domain -Type A
Resolve-DnsName $Domain -Type AAAA
Resolve-DnsName $Domain -Type MX
Resolve-DnsName $Domain -Type TXT
Resolve-DnsName "www.$Domain" -Type A
Resolve-DnsName "api.$Domain" -Type A
Resolve-DnsName "mail.$Domain" -Type A
```

The Windows source should also remain undisclosed to the SOC Analyst during the live investigation.

## 6. Suggested live sequence

Do not announce the exact start time. A practical attacker-side run is:

```text
T+00  Record attacker UTC time and public IP privately
T+01  NS + SOA queries
T+02  A / AAAA / MX / NS / TXT against apex
T+03  Enumerate several plausible subdomains
T+05  AXFR check
T+06  Optional HTTPS HEAD requests to discovered public host
T+07  Stop active reconnaissance
T+08  Record end time and preserve outputs
```

This timing is guidance, not a fixed signature. The attacker may vary pauses/order so the exercise is not scripted around the detection rule.

## 7. Ground truth to preserve privately

Record:

- UTC start and end time;
- attacker public IP(s);
- system used (Kali / Windows);
- commands issued;
- authoritative nameserver used;
- queried names;
- query types;
- AXFR result;
- any HTTPS follow-up;
- screenshots or terminal output;
- any unexpected behavior.

Use [`ground-truth-template.md`](ground-truth-template.md).

Do **not** commit the completed ground-truth record to the public scenario repository before the SOC disposition is finalized.

## 8. Stop conditions

Stop the attacker activity if:

- the public lab namespace behaves unexpectedly;
- query volume becomes excessive;
- any action leaves the defined DNS/web reconnaissance boundary;
- defender platform health is degraded;
- the Project Lead determines the exercise cannot be completed safely.

## 9. What success means from the attacker side

The attacker has succeeded in the reconnaissance objective if they can build a defensible public DNS picture such as:

```text
zone authority
+ public hostnames
+ record types
+ address/mail/TXT exposure
+ failed or successful AXFR result
+ optional discovered web endpoint
```

Success does **not** require compromise. Scenario 01 ends at reconnaissance.

---

<div align="center">

[🏠 Scenario Home](../README.md) · [📁 Attacker](README.md) · [⬆ Back to top](#top)

<sub>DNSentinel Lab · Evidence-first DNS security engineering</sub>

</div>
