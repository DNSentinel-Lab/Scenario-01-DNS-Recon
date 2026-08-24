<a id="top"></a>

> 🧭 [Scenario 01](../README.md) › [Attacker](README.md) › **Scenario 01 — Completed Private Adversary Ground Truth**

![Scenario](https://img.shields.io/badge/Scenario_01-Complete-2EA44F?style=flat-square)
![DNSentinel](https://img.shields.io/badge/DNSentinel-Technical_Record-00F5FF?style=flat-square)

---

# Scenario 01 — Completed Private Adversary Ground Truth

> This record was kept separate from the defender investigation during live execution. It was completed after the SOC disposition and Incident Response handoff/decision were locked.

## Exercise identity

| Field | Value |
|---|---|
| Exercise date | 2026-08-23 |
| Operator | Abdul-Rehman |
| Target | `soclab.abdul4rehman215.tech` |
| Primary attacker platform | External Kali EC2 |
| Secondary attacker platform | Not used in the official external reconnaissance run |
| Attacker AWS account | Separate external account — account ID intentionally not published |
| Scenario mapping | `T1590.002 — Gather Victim Network Information: DNS` |
| Cyber Kill Chain | Reconnaissance |

## Case 01 — Authorized DNS validation control

Case 01 was intentionally generated from a defender-owned asset to test whether the production detection could distinguish **reconnaissance-like behavior** from approved operational activity once business context was added.

| Field | Value |
|---|---|
| Source asset | `dns-soc-web01` |
| Observed public source | `100.49.192.164` |
| Purpose | Authorized post-change DNS validation |
| UTC activity time | 2026-08-23 08:41:55 |
| Query count | 16 |
| Unique names | 4 |
| Record types | A, AAAA, TXT, CNAME |
| Final SOC outcome | Authorized / Benign True Positive |
| IR escalation | No |

### Case 01 names

- `soclab.abdul4rehman215.tech`
- `www.soclab.abdul4rehman215.tech`
- `api.soclab.abdul4rehman215.tech`
- `mail.soclab.abdul4rehman215.tech`

### Case 01 execution pattern

```bash
for host in \
  soclab.abdul4rehman215.tech \
  www.soclab.abdul4rehman215.tech \
  api.soclab.abdul4rehman215.tech \
  mail.soclab.abdul4rehman215.tech; do
  for type in A AAAA TXT CNAME; do
    dig @"$AUTH_NS" "$host" "$type" +noall +answer
  done
done
```

**Ground-truth purpose:** generate a legitimate 4-name × 4-type authoritative DNS validation burst without telling the SOC Analyst the business context in advance.

---

## Case 02 — External DNS reconnaissance

### Timing

| Field | UTC time |
|---|---|
| Start | 2026-08-23 10:21:51 |
| First authoritative DNS query | 2026-08-23 10:21:51 |
| Last authoritative DNS query | 2026-08-23 10:22:34 |
| Optional web follow-up | Not performed as part of the official DNS reconnaissance sequence |
| End | 2026-08-23 10:22:34 |

### Source identity

| Field | Value |
|---|---|
| Kali public IP | `44.222.207.148` |
| Windows public IP | Not used |
| Authoritative NS used | `ns-1750.awsdns-26.co.uk` |
| Target public A record observed during reconnaissance | `100.49.192.164` |

### Commands / actions

| UTC | Source | Action / command | Target | Result |
|---|---|---|---|---|
| 10:21:51 | External Kali | `dig "$DOMAIN" NS` and `dig "$DOMAIN" SOA` | `soclab.abdul4rehman215.tech` | Route 53 authority and SOA metadata identified |
| 10:21:51 | External Kali | Direct authoritative A/AAAA/MX/NS/SOA/TXT interrogation | Zone apex | Six DNS record types checked against the authoritative server |
| ~10:22:10 | External Kali | A-record enumeration across the controlled service-name list | 16 public/service-style names | Existing names returned NOERROR; most guesses returned NXDOMAIN |
| ~10:22:20 | External Kali | TXT enumeration across the service-name list | Controlled namespace | TXT/authority behavior checked; most guessed labels did not exist |
| ~10:22:30 | External Kali | A-record re-check across the service-name list | Controlled namespace | Repeated breadth confirmed the reconnaissance pattern |
| During recon | External Kali | `dig @"$AUTH" "$DOMAIN" AXFR +time=3 +tries=1` | Authoritative Route 53 DNS | No successful zone transfer / no zone contents exposed |

### Command set used

```bash
export DOMAIN="soclab.abdul4rehman215.tech"
export AUTH="ns-1750.awsdns-26.co.uk"

# Authority / zone discovery
dig "$DOMAIN" NS
dig "$DOMAIN" SOA

# Direct authoritative record interrogation
for type in A AAAA MX NS SOA TXT; do
  dig @"$AUTH" "$DOMAIN" "$type"
done

# Controlled service-name enumeration
cat > /tmp/dns_words.txt <<'NAMES'
www
api
dev
test
stage
staging
prod
admin
vpn
mail
portal
internal
db
app
backup
monitor
NAMES

while read -r sub; do
  dig @"$AUTH" "${sub}.${DOMAIN}" A +noall +answer +authority
done < /tmp/dns_words.txt

while read -r sub; do
  dig @"$AUTH" "${sub}.${DOMAIN}" TXT +noall +answer +authority
done < /tmp/dns_words.txt

# Safe zone-transfer posture check
dig @"$AUTH" "$DOMAIN" AXFR +time=3 +tries=1
```

## Ground-truth behavior summary

- **Names queried:** zone apex plus the controlled labels `www`, `api`, `dev`, `test`, `stage`, `staging`, `prod`, `admin`, `vpn`, `mail`, `portal`, `internal`, `db`, `app`, `backup`, and `monitor`.
- **Defender-observed unique names:** 17.
- **Record types observed by the defender:** A, AAAA, MX, NS, SOA, TXT.
- **Defender-observed query count:** 53.
- **Defender-observed response pattern:** 44 NXDOMAIN / 9 NOERROR.
- **NXDOMAIN names attempted:** service/environment guesses such as `api`, `dev`, `test`, `stage`, `staging`, `prod`, `admin`, `vpn`, `mail`, `portal`, `internal`, `db`, `app`, `backup`, and `monitor` where records did not exist.
- **AXFR result:** no successful zone transfer; the public authoritative service did not expose zone contents through AXFR.
- **Web follow-up performed:** no attacker-side web follow-up was required for the official Case 02 DNS reconnaissance sequence.
- **Behavior intentionally varied:** the sequence moved from zone/authority discovery to broad A enumeration, TXT interrogation and repeated A checks. Query order and timing were not disclosed to the SOC Analyst.

## Defender comparison after reveal

| Perspective | Final observation |
|---|---|
| Adversary | External DNS reconnaissance against the owned public namespace |
| Detection v1.0 | Production reconnaissance detection triggered |
| AI | Suggested DNS reconnaissance / `T1590.002`, with human validation required |
| SOC | True Positive — Suspicious / Likely Unauthorized DNS Reconnaissance; High confidence |
| IR | Reconnaissance confirmed; no proven progression; Preserve + Monitor; no active containment |

## Reveal checklist

- [x] SOC disposition recorded first
- [x] AI validation recorded first
- [x] IR handoff recorded when escalation occurred
- [x] Detection Engineer did not change v1.0 during the live run
- [x] IR decision was recorded before final attacker/defender comparison
- [x] Ground truth compared with defender evidence only after the above gates

## Related evidence

- [`PROJECT-LEAD-ADVERSARY.md`](PROJECT-LEAD-ADVERSARY.md)
- [`SCENARIO-01-ADVERSARY-PLAYBOOK.md`](SCENARIO-01-ADVERSARY-PLAYBOOK.md)
- [`../soc/case-01-soc-investigation-closure.md`](../soc/case-01-soc-investigation-closure.md)
- [`../soc/case-02-soc-investigation-ir-handoff.md`](../soc/case-02-soc-investigation-ir-handoff.md)
- [`../exercise/final-comparison.md`](../exercise/final-comparison.md)

---

<div align="center">

[🏠 Scenario Home](../README.md) · [📁 Attacker](README.md) · [⬆ Back to top](#top)

<sub>DNSentinel Lab · Evidence-first DNS security engineering</sub>

</div>
