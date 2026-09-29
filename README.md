# Splunk-Bots-CTF V1

<h1>1.1 Find the brand name of the vulnerability scanner.</h1>
ANSWER: Acunetix Web Vulnerability Scanner<br>
First contact (2016-08-10 14:36:48.122)<br>
Last contact (2016-08-10 15:22:27.612)<br>
source: 40.80.148.42<br>
destination: 192.168.250.70 | 192.168.250.40<br>
<img width="308" height="23" alt="image" src="https://github.com/user-attachments/assets/3e7ef01e-6445-4e38-bf50-ba686b5e0113" /><br>
NOTE: check the uri field to see what attacker is attempting to exploit<br>
<img width="595" height="226" alt="image" src="https://github.com/user-attachments/assets/cbe5be4f-c5c7-4ca6-b911-54f396065ed7" /><br>
NOTE: in the interesting fields section, notice that the specific uri path has a suspiciously large ammount of logs<br>
<img width="593" height="29" alt="image" src="https://github.com/user-attachments/assets/4a651127-8209-4874-96d2-47e407b0fa93" /><br>
<img width="1431" height="438" alt="image" src="https://github.com/user-attachments/assets/b584e7b7-fa37-45f2-8775-a138e7609ccf" /><br>


<h1>1.2 Find the attacker’s IP address</h1>
ANSWER: 40.80.148.42<br>
<img width="585" height="226" alt="image" src="https://github.com/user-attachments/assets/688e9f61-d1b3-4f50-817d-5175442d9532" /><br>
Note: we can continue from the prior query, check the src_ip from there
