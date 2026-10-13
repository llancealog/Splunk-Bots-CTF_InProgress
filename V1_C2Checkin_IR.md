# V1: C2 Checkin
Incident Report: IR-2016-0824 Ransomware Check-in & Lateral Spread

**Severity:** High | **Status:** For Escalation | **Analyst:** Lance Alog

---

## 1. Executive Summary
On `2016-08-24 09:49:24.542` workstation `we8105desk` (`192.168.250.100`) triggered a Suricata alert with the signature ETPRO TROJAN Ransomware/Cerber Checkin 2. Initial triage verifies the contents of the alert, where the workstation can be seen reaching out to each IP within the `85.93.0.0` subnet via UDP. Further investigation after the alert also shows high volume of automated activity from SMB logs targeted towards the Domain Controller (`192.168.250.20`) coming from the workstation at `2016-08-24 09:49:52.315.` Host isolation has been requested and the issue is to be escalated to Tier 2 / Incident Response.

---

## 2. Incident Timeline (UTC)

| Time (UTC) | Source / Layer | Event Description |
| :--- | :--- | :--- |
| **09:49:24** | Suricata IDS | Alert triggered: `ETPRO TROJAN Ransomware/Cerber Checkin 2` (SID: `2816763`). |
| **09:49:24** | Network (`stream:ip`) | Outbound UDP spray commences targeting external subnet `85.93.0.0/16`. |
| **09:49:52** | Network (`stream:smb`) | Automated millisecond-interval `smb2 create` commands target DC shares (`192.168.250.20`). |

---

## 3. Evidence & Corroboration

### A. Network C2 Activity (`stream:ip`)
The compromised host initiated high-volume automated UDP traffic across external destination IP `85.93.0.0/16` 
```spl
index="botsv1" (host="we8105desk" OR src_ip="192.168.250.100" OR src="192.168.250.100") sourcetype="stream:ip" dest_ip="85.93.*"
| table _time dest_ip dest_port protocol
```
*Result: Over 47,000 events matching automated behavior such as millisecond intervals between multiple events.*

### B. Lateral Movement & Share Impact (`stream:smb`)
High-frequency SMB requests directed at the Domain Controller (`192.168.250.20`):
```spl
index="botsv1" (host="we8105desk" OR src_ip="192.168.250.100" OR src="192.168.250.100") sourcetype="stream:smb" dest_ip="192.168.250.20" "command{}"="*" filename=*
| sort _time
| table _time filename command{} src_ip dest_ip
```
*Result: Millisecond intervals between `smb2 create` events, indicating automated activity.*

---

## 4. Indicators of Compromise (IOCs)

| Type | Indicator | Context |
| :--- | :--- | :--- |
| **Internal Host** | `we8105desk` (`192.168.250.100`) | Patient Zero |
| **External Subnet** | `85.93.0.0/16` (UDP) | C2 Checkin Destination Subnet |
| **Internal Target** | `192.168.250.20:445` | Domain Controller & its SMB Shares |

---

## 5. Verdict & Next Steps

* **Verdict:** **True Positive** — C2 Checkin Alert Confirmed / Suspected Active Ransomware .
* **Actions Taken:** 
  * Requested immediate network isolation of `we8105desk`.
  * Verified enterprise scope (no other infected internal hosts observed).
* **Recommended Next Steps (Tier 2 / IR):**
  * Inspect network shares on `192.168.250.20` for modified or encrypted files.
  * Perform Root Cause Analysis (RCA) on `we8105desk` (prior to `09:49 UTC`) to identify initial entry vector and malicious payload.
