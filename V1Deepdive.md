# GENERAL OVERVIEW & IR Lifecycle (Bob's USB Incident)

<img width="550" height="578" alt="image" src="https://github.com/user-attachments/assets/833454e2-29dc-4a7f-9f6b-1c37abbe55f6" />

## Relevant Info:
- we8105desk: 192.168.250.100 (Bob's Workstation)
- DC: 192.168.250.20
- Website/WebServer: 192.168.250.70
- Default Gateway: 192.168.250.1
- Broadcast Address: 192.168.250.255

## 01: Preparation (Recommendations)
- Update playbooks
- Assign team roles & responsibilities
- Setup tools EDR, IDS, forwarders
- Prepare and train employees in events of an incident
- Document baselines for normal network and asset standards

## 02.1: Discovery & Verification (Following Leads)

<img width="1771" height="191" alt="image" src="https://github.com/user-attachments/assets/5588f1a1-a411-45ec-9665-31580036f2cc" />

- 2016-08-24 09:49:24.542 (Cerber Checkin)

- This alert confirms Cerber ransomware finished infecting the computer and is attempting to contact the C2 (Command & Control) server to report that the attack succeeded.

- Verify if the event is real or just a false positive

### 1. Gather relevant info (when, description, src IP, dest IP)

<img width="1745" height="66" alt="image" src="https://github.com/user-attachments/assets/0b0760c0-25bf-4634-ab30-542866e7f4b8" />

### 2. Check other log sources to verify the event actually occurred

<img width="586" height="169" alt="image" src="https://github.com/user-attachments/assets/c57b5861-e595-4ebc-b28a-df366a8b61b4" />

*(Network Layer)* Check if log sources like `stream:ip` or `stream:udp` recorded traffic from the src and dest IP from the alert

<img width="598" height="278" alt="image" src="https://github.com/user-attachments/assets/492c23a3-25a4-4867-b92c-a0d8373211ce" />

*(Host Layer)* Check Sysmon if a process actually initiated an outbound network connection to that IP, `EventDescription = Network Connect`, or `EventID = 3`

<img width="943" height="135" alt="image" src="https://github.com/user-attachments/assets/04391ed5-e22f-46cb-9c96-618cf642020a" />

- Basically verify network and endpoint logs for the occurrence of the event (In this case we can verify it as a true positive)

### 3. After verifying suggest quarantine (03: Containment & Continuity) on the infected device, then do a quick check on other devices within the network for similar patterns or suspicious destinations

<img width="1916" height="429" alt="image" src="https://github.com/user-attachments/assets/fa7f3da4-1de6-437b-b138-4a1e949aa70c" />

- The table verifies that `192.168.250.100` was the only case within the enterprise, allowing us to proceed with the next steps. Though if other devices were found we would also need to quarantine them.

### 4. Proceed with investigating `192.168.250.100` for attempts to spread across the local network

- Check the log sources and investigate based on the volume of traffic, prioritize network traffic (`stream:ip`, `smb`, `tcp`, etc.) first, before host traffic (`wineventlog`, `Sysmon`, `winregistry`) 

<img width="1914" height="687" alt="image" src="https://github.com/user-attachments/assets/86a53ec9-e0f4-40de-86b3-2861235f4b10" />

- Let's begin checking `stream:ip` as it has the most volume for network traffic

<img width="1172" height="944" alt="image" src="https://github.com/user-attachments/assets/a91e5b65-8b1d-426b-93aa-d4fe749252e5" />

- From this info we can validate the alert targeting the entire `85.93.0.0/16` subnet, which is an IOA that would allow us to classify the alert as a true positive
- `ADDITIONAL RESEARCH (but, we can already use the external IP subnet spraying as an IOA)`

<img width="963" height="874" alt="image" src="https://github.com/user-attachments/assets/b4224f50-e0bc-4a4d-ba42-427066d23105" />

- As we could see from an overview of the `dest_ip` field, most of the traffic would probably be from the outbound traffic to the `85.93.0.0/16` subnet; since we already knew that, we could filter that out for our investigation

<img width="1082" height="873" alt="image" src="https://github.com/user-attachments/assets/51416688-5d76-4305-8eb4-ac92bb5691ce" />

- This shows us that most of the traffic is happening within the internal network—in this case towards the DC. Now let's pivot back and check other log sources with more detailed info

<img width="599" height="508" alt="image" src="https://github.com/user-attachments/assets/80ad37aa-f950-4abd-b94a-d8dc4ab80474" />

- In this case, the next log source for network traffic that generated high volume is from `stream:smb`

<img width="1703" height="866" alt="image" src="https://github.com/user-attachments/assets/9b9ed557-c3ae-4de3-aa19-00111979f388" />

- We decided to check the command in the SMB logs with the most volume, and from what we could see here it is a strong indicator of high-volume automated activity, with the amount of logs generated and the time between each log being in milliseconds

### 5. Create an incident report for escalation after verifying the alert is a true positive
- Check the incident reports named `V1: C2 Checkin` for a sample.

---

## 02.2: Discovery & Verification (Root Cause Analysis)
### 1. Backtrack from your alert prioritize Host traffic like `Sysmon`

<img width="1288" height="42" alt="image" src="https://github.com/user-attachments/assets/bc3a26ed-5cc2-4782-af6e-7252305d6e81" />

- Query your logs based on the info from the alert and investigate the earliest log
- Make sure to use `EventCode` 3 as it signifies Network Connect logs

<img width="935" height="36" alt="image" src="https://github.com/user-attachments/assets/38f9f720-6e27-4201-8b58-da661b415db1" />

- Check the fields in the earlies logs and find the `Image` field, since its the full system path of the executable that triggered the event

<img width="882" height="89" alt="image" src="https://github.com/user-attachments/assets/62954971-e301-4cae-a671-57e74cd81c9b" />

- Also take note of `ProcessId` or `ProcessGuid` as it is a unique identifier for the process that executed leading to the creation of those 40,000+ logs / network traffic
- Preferrably note `ProcessGuid` since `ProcessId` maybe recycled by Windows overtime

<img width="1680" height="65" alt="image" src="https://github.com/user-attachments/assets/5cff47cd-3d5c-495c-8304-5bdd7dfd8f8c" />

- Now, look for the origin of that image by adding both the `image` and `EventCode` 1 (event id that shows the image and what launched it) to the query



---

## 03: Containment & Continuity (Recommendations)
- Realistically in this scenario we would want to isolate `192.168.250.100`, basically cutting it off from the network.
