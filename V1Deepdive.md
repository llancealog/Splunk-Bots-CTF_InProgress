<h1>GENERAL OVERVIEW & IR Lifecycle (Bob's USB Incident)</h1>
<img width="550" height="578" alt="image" src="https://github.com/user-attachments/assets/833454e2-29dc-4a7f-9f6b-1c37abbe55f6" /><br>
<h2>Relevant Info: </h2>
&bull; we8105desk: 192.168.250.100 (Bob's Workstation)<br>

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
3. After the alert check the infected workstation's next actions or activity<br>
<img width="760" height="47" alt="image" src="https://github.com/user-attachments/assets/35eb537c-666a-4175-a2c8-9b1bb7f2382f" /><br>
<img width="1422" height="534" alt="image" src="https://github.com/user-attachments/assets/834bbb3c-15d2-493d-81de-97b16697b2bc" /><br>
&bull; First check for volume,  what type of logs did it generate the most in the time past that alert, in this case lets check <b><code>stream:ip</code></b><br>
&bull; Upon checking the destinations of the stream_ip it seems that most of it verifies the alert of traffic coming from 192.168.250.100 to the 85.93.0.0 subnet.<br>
<img width="1075" height="46" alt="image" src="https://github.com/user-attachments/assets/a2c9f8f4-f89d-448b-b6de-e78ecd93d901" /><br>
&bull; Now we filter that subnet out of our query

<br>
<h2>02.2: Discovery & Verification (Root Cause Analysis)</h2>

<br><br>
<h2>03: Containment & Continuity (Recommendations)</h2>
&bull; Realistically in this scenario we would want to isolate 192.168.250.100, basically cutting it off from the network.
