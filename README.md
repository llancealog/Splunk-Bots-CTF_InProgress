# Splunk-Bots-CTF V1

<h1>1.1 Find the brand name of the vulnerability scanner.</h1>
<h2>ANSWER: Acunetix Web Vulnerability Scanner</h2>
First contact (2016-08-10 14:36:48.122)<br>
Last contact (2016-08-10 15:22:27.612)<br>
source: 40.80.148.42<br>
destination: 192.168.250.70 | 192.168.250.40<br>
<img width="308" height="23" alt="image" src="https://github.com/user-attachments/assets/3e7ef01e-6445-4e38-bf50-ba686b5e0113" /><br>
NOTE: check the uri field to see what attacker is attempting to exploit<br>
<img width="595" height="226" alt="image" src="https://github.com/user-attachments/assets/cbe5be4f-c5c7-4ca6-b911-54f396065ed7" /><br>
NOTE: in the interesting fields section, notice that the specific uri path has a suspiciously large ammount of logs<br>
<img width="593" height="29" alt="image" src="https://github.com/user-attachments/assets/4a651127-8209-4874-96d2-47e407b0fa93" /><br>
<img width="1431" height="438" alt="image" src="https://github.com/user-attachments/assets/b584e7b7-fa37-45f2-8775-a138e7609ccf" /><br><br>


<h1>1.2 Find the attacker’s IP address</h1>
ANSWER: 40.80.148.42<br>
<img width="585" height="226" alt="image" src="https://github.com/user-attachments/assets/688e9f61-d1b3-4f50-817d-5175442d9532" /><br>
NOTE: based on the previous query, inspecting the src_ip field confirms where the traffic originated from.<br><br>


<h1>1.3 Find the IP address of the web server serving "imreallynotbatman.com".</h1>
ANSWER: 192.168.250.70<br>
<img width="488" height="28" alt="image" src="https://github.com/user-attachments/assets/b0461239-1b7d-46b9-8043-e034cdb261eb" /><br>
NOTE: add the website name in the query, and check the dest_ip field to find the IP hosting the site<br>
<img width="581" height="226" alt="image" src="https://github.com/user-attachments/assets/217b8bfd-434d-496e-913a-a28975ad3a93" /><br><br>


<h1>1.4 Find the name of the file used to deface the web server serving "imreallynotbatman.com".</h1>
ANSWER: poisonivy-is-coming-for-you-batman.jpeg<br>
<img width="395" height="24" alt="image" src="https://github.com/user-attachments/assets/e823a08b-e596-4788-b160-3349519dd142" /><br>
NOTE: we can continue from the sourcetype=stream:http<br>
<img width="596" height="316" alt="image" src="https://github.com/user-attachments/assets/7f1f1707-fe39-4ef3-8e47-fb6125bdb952" /><br>
NOTE: check for src_ip to narrow the attackers, notice the web server's IP is included which is a red flag. Web servers are supposed to be passive and just responds, not the one that initiates<br>
<img width="552" height="23" alt="image" src="https://github.com/user-attachments/assets/0c18163b-4145-4473-924d-079a2a5cb556" /><br>
NOTE: add the server's IP as the src_ip in your query, to cut down on fields displayed<br>
<img width="518" height="260" alt="image" src="https://github.com/user-attachments/assets/8373666e-757a-4320-a92f-1a2b772632fc" /><br>
NOTE: check the packets and relevant fields such as the uri and the src_headers (contains the initiator's request)





