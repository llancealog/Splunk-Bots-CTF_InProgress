<h1>GENERAL OVERVIEW & IR Lifecycle (Bob's USB Incident)</h1>
<img width="550" height="578" alt="image" src="https://github.com/user-attachments/assets/833454e2-29dc-4a7f-9f6b-1c37abbe55f6" /><br>
<h2>Relevant Info: </h2>
&bull; we8105desk: 192.168.250.100 (Bob's Workstation)<br>

<h2>01: Preparation </h2>
&bull; update playbooks<br>
&bull; assign team roles & responsibilities<br>
&bull; setup tools EDR, IDS, forwarders<br>
&bull; prepare and train employees in events of an incident<br>
&bull; document baselines for normal network and asset standards<br><br>

<h2>02: Discovery & Verification</h2>
<img width="1771" height="191" alt="image" src="https://github.com/user-attachments/assets/5588f1a1-a411-45ec-9665-31580036f2cc" /><br>
&bull; 2016-08-24 09:49:24.542 (Cerber Checkin)<br>
&bull; This alert confirms Cerber ransomware finished infecting the computer and is attempting to contact the C2 (Command & Control) server to report that the attack succeeded.<br>
&bull; Realistically in this scenario we would want to isolate 192.168.250.100, basically cutting it off from the network.
