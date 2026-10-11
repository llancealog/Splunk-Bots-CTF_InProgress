<h1>GENERAL OVERVIEW & IR Lifecycle (Bob's USB Incident)</h1>
<img width="550" height="578" alt="image" src="https://github.com/user-attachments/assets/833454e2-29dc-4a7f-9f6b-1c37abbe55f6" /><br>
<h2>Relevant Info: </h2>
&bull; we8105desk: 192.168.250.100 (Bob's Workstation)<br>
&bull; DC: 192.168.250.20<br>
&bull; Website/WebServer: 192.168.250.70<br>
&bull; Default Gateway:192.168.250.1<br>
&bull; Broadcast Address: 192.168.250.255<br>

<h2>01: Preparation (Recommendations)</h2>
&bull; update playbooks<br>
&bull; assign team roles & responsibilities<br>
&bull; setup tools EDR, IDS, forwarders<br>
&bull; prepare and train employees in events of an incident<br>
&bull; document baselines for normal network and asset standards<br><br>

<h2>02.1: Discovery & Verification (Following Leads)</h2>
<img width="1771" height="191" alt="image" src="https://github.com/user-attachments/assets/5588f1a1-a411-45ec-9665-31580036f2cc" /><br>
&bull; 2016-08-24 09:49:24.542 (Cerber Checkin)<br><br>
&bull; This alert confirms Cerber ransomware finished infecting the computer and is attempting to contact the C2 (Command & Control) server to report that the attack succeeded.<br><br>
&bull; Verify if the event is real or just a false positive<br>
1. Gather relevant info (when, description, src IP, dest IP)<br>
<img width="1745" height="66" alt="image" src="https://github.com/user-attachments/assets/0b0760c0-25bf-4634-ab30-542866e7f4b8" /><br>

2. Check other log sources to verify the event actually occurred<br>
<img width="586" height="169" alt="image" src="https://github.com/user-attachments/assets/c57b5861-e595-4ebc-b28a-df366a8b61b4" /><br>
(Network Layer) Check if log sources like stream:ip or stream:udp recorded traffic from the src and dest IP from the alert<br>
<img width="598" height="278" alt="image" src="https://github.com/user-attachments/assets/492c23a3-25a4-4867-b92c-a0d8373211ce" /><br>
(Host Layer) Check sysmon if a process actually initiated an outbound network connection to that IP, <b><code>EventDescription</code></b>= Network Connect, or <b><code>EventID</code></b>= 3<br>
<img width="943" height="135" alt="image" src="https://github.com/user-attachments/assets/04391ed5-e22f-46cb-9c96-618cf642020a" /><br>
&bull; Basically verify network and endpoint logs for the occurrence of the event (In this case we can verify it as a true positive)<br><br>

3. After verifying suggest quarantine (03: Containment & Continuity) on the infected device, Then do a quick check on other devices within the network for similar patterns or suspicious destinations<br>
<img width="1916" height="429" alt="image" src="https://github.com/user-attachments/assets/fa7f3da4-1de6-437b-b138-4a1e949aa70c" /><br>
&bull; The table verifies that <b><code>192.168.250.100</code></b> was the only case within the enterprise, allowing us to proceed with the next steps. Though if other devices where found we would also need to quarantine them.<br><br>

4. Proceed with investigating <b><code>192.168.250.100</code></b> for attempts to spread across the local network.<br>
&bull; Check the log sources and investigate based on the volume of traffic, prioritize network traffic (<b><code>stream:ip, smb, tcp, etc.</code></b>) first, before host traffic (<b><code>wineventlog, Sysmon, winregistry</code></b>)<br>
<img width="1914" height="687" alt="image" src="https://github.com/user-attachments/assets/86a53ec9-e0f4-40de-86b3-2861235f4b10" /><br>
&bull; Let's begin checking <b><code>stream:ip</code></b> as it has the most volume for network traffic<br>
<img width="1172" height="944" alt="image" src="https://github.com/user-attachments/assets/a91e5b65-8b1d-426b-93aa-d4fe749252e5" /><br>
&bull; From these info we can validate the alert targeting the entire 85.94.0.0 subnet, which is an IOC that would allow us to classify the alert as a true positive<br>
&bull; <b><code>ADDITIONAL RESEARCH (but, we can already use the external IP subnet spraying as an IOC)</code></b>
<img width="963" height="874" alt="image" src="https://github.com/user-attachments/assets/b4224f50-e0bc-4a4d-ba42-427066d23105" /><br>
&bull; As we could see from an overview of the dest_ip field most of the traffic would probably be from the outbound traffic to the 85.93.0.0 subnet, since we already knew that we could filter that out for our investigation<br>
<img width="1082" height="873" alt="image" src="https://github.com/user-attachments/assets/51416688-5d76-4305-8eb4-ac92bb5691ce" /><br>
&bull; This shows us that most of the traffic are happening within the internal network, in this case it was towards the DC, now lets pivot back and check other log sources with more detailed info<br>
<img width="599" height="508" alt="image" src="https://github.com/user-attachments/assets/80ad37aa-f950-4abd-b94a-d8dc4ab80474" /><br>
&bull; In this case the next log source for network traffic that generated high volume is from <b><code>stream:smb</code></b>

<br>
<h2>02.2: Discovery & Verification (Root Cause Analysis)</h2>

<br><br>
<h2>03: Containment & Continuity (Recommendations)</h2>
&bull; Realistically in this scenario we would want to isolate 192.168.250.100, basically cutting it off from the network.
