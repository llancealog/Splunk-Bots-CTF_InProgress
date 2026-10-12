<h1>V1: C2 Checkin</h1>
Incident Report: IR-2016-0824 Ransomware Check-in & Lateral Spread

**Severity:** High | **Status:** For Escalation | **Analyst:** [Lance Alog]  

---

## 1. Executive Summary
On `2016-08-24 09:49:24.542` workstation `we8105desk` (`192.168.250.100`) triggered a Suricata alert with the signature ETPRO TROJAN Ransomware/Cerber Checkin 2. Initial triage verifies the contents of the alert, where the workstation can be seen reaching out to each IP within the `85.93.0.0` subnet via UDP. Further investigation after the alert also shows high volume of automated activity from SMB logs targeted towards the Domain Controller (`192.168.250.20`) coming from the workstation at `2016-08-24 09:49:52.315.` Host isolation has been requested and the issue is to be escalated to Tier 2 / Incident Response.

---

## 2. Incident Timeline (UTC)

| Time (UTC) | Source / Layer | Event Description |
| :--- | :--- | :--- |
| **09:49:24** | Suricata IDS | Alert triggered: `ETPRO TROJAN Ransomware/Cerber Checkin 2` (SID: `2816763`). |
| **09:49:24** | Sysmon (EID 3) | `osk.exe` spawned from `AppData\Roaming\{GUID}\` initiates network connections. |
| **09:49:25** | Network (`stream:ip`) | Outbound UDP spray commences targeting external subnet `85.93.0.0/16`. |
| **09:49:52** | Network (`stream:smb`) | Automated millisecond-interval `smb2 create` commands target DC shares (`192.168.250.20`). |
| **[Triage Time]** | SIEM Scoping | Confirmed no other internal enterprise endpoints communicated with `85.93.0.0/16`. |
| **[Handoff Time]** | SOC Tier 1 | Host isolation requested; ticket escalated to Tier 2 / DFIR. |

---

## 3. Evidence & Corroboration

### A. Network C2 Activity (`stream:ip`)
The compromised host initiated high-volume UDP bursts across external destination IP block `85.93.0.0/16` on non-standard ports (e.g., `6892`).
```spl
index="botsv1" src_ip="192.168.250.100" sourcetype="stream:ip" dest_ip="85.93.*"
| stats count by dest_ip, dest_port, transport
```
*Result: Over 47,000 events matching automated C2 spray behavior.*

### B. Endpoint Process Validation (Sysmon)
Sysmon EventCode 3 (`Network Connect`) confirmed the process initiating outbound connections was masquerading as the Windows On-Screen Keyboard from an abnormal path:
* **Path:** `C:\Users\bob.smith.WAYNECORPINC\AppData\Roaming\{35ACA89F-933F-6A5D-2776-A3589FB99832}\osk.exe`

### C. Lateral Movement & Share Impact (`stream:smb`)
High-frequency SMBv2 requests directed at the Domain Controller (`192.168.250.20`):
```spl
index="botsv1" src_ip="192.168.250.100" dest_ip="192.168.250.20" sourcetype="stream:smb" command="smb2 create"
| table _time, filename, command, src_ip, dest_ip
```
*Result: Millisecond-frequency handle creation targeting network shares, indicating automated traversal.*

---

## 4. Indicators of Compromise (IOCs)

| Type | Indicator | Context |
| :--- | :--- | :--- |
| **Internal Host** | `we8105desk` (`192.168.250.100`) | Patient Zero |
| **External Subnet** | `85.93.0.0/16` (UDP) | C2 Destination Subnet |
| **Malicious Binary** | `osk.exe` | Masqueraded executable in user `AppData` |
| **Internal Target** | `192.168.250.20:445` | Targeted DC / SMB Shares |

---

## 5. Verdict & Next Steps

* **Verdict:** **True Positive** — Suspected Active Ransomware / Lateral Propagation.
* **Actions Taken:** 
  * Requested immediate network isolation of `we8105desk`.
  * Verified enterprise scope (no other infected internal hosts observed).
* **Recommended Next Steps (Tier 2 / IR):**
  1. Inspect network shares on `192.168.250.20` for modified or encrypted files.
  2. Perform Root Cause Analysis (RCA) on `we8105desk` (review web proxy, DNS, and email delivery prior to `09:49 UTC`) to identify initial entry vector.
  3. Extract payload hash from `we8105desk` disk image for submission to threat intelligence and perimeter blocking.
