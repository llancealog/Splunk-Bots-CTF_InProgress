# Splunk-Bots-CTF V1

<h1>1.1 Find the brand name of the vulnerability scanner.</h1>
<h2>ANSWER: Acunetix Web Vulnerability Scanner</h2>
First contact (2016-08-10 14:36:48.122)<br>
Last contact (2016-08-10 15:22:27.612)<br>
source: 40.80.148.42<br>
destination: 192.168.250.70 | 192.168.250.40<br>
<img width="308" height="23" alt="image" src="https://github.com/user-attachments/assets/3e7ef01e-6445-4e38-bf50-ba686b5e0113" /><br>
&bull; check the uri field to see what attacker is attempting to exploit<br>
<img width="595" height="226" alt="image" src="https://github.com/user-attachments/assets/cbe5be4f-c5c7-4ca6-b911-54f396065ed7" /><br>
&bull; in the interesting fields section, notice that the specific uri path has a suspiciously large ammount of logs<br>
<img width="593" height="29" alt="image" src="https://github.com/user-attachments/assets/4a651127-8209-4874-96d2-47e407b0fa93" /><br>
<img width="1431" height="438" alt="image" src="https://github.com/user-attachments/assets/b584e7b7-fa37-45f2-8775-a138e7609ccf" /><br><br>


<h1>1.2 Find the attacker’s IP address</h1>
<h2>ANSWER: 40.80.148.42</h2>
<img width="585" height="226" alt="image" src="https://github.com/user-attachments/assets/688e9f61-d1b3-4f50-817d-5175442d9532" /><br>
&bull; based on the previous query, inspecting the src_ip field confirms where the traffic originated from.<br><br>


<h1>1.3 Find the IP address of the web server serving "imreallynotbatman.com".</h1>
<h2>ANSWER: 192.168.250.70</h2>
<img width="488" height="28" alt="image" src="https://github.com/user-attachments/assets/b0461239-1b7d-46b9-8043-e034cdb261eb" /><br>
&bull; add the website name in the query, and check the dest_ip field to find the IP hosting the site<br>
<img width="581" height="226" alt="image" src="https://github.com/user-attachments/assets/217b8bfd-434d-496e-913a-a28975ad3a93" /><br><br>


<h1>1.4 Find the name of the file used to deface the web server serving "imreallynotbatman.com".</h1>
<h2>ANSWER: poisonivy-is-coming-for-you-batman.jpeg</h2>
<img width="395" height="24" alt="image" src="https://github.com/user-attachments/assets/e823a08b-e596-4788-b160-3349519dd142" /><br>
&bull; we can continue from the sourcetype=stream:http<br>
<img width="596" height="316" alt="image" src="https://github.com/user-attachments/assets/7f1f1707-fe39-4ef3-8e47-fb6125bdb952" /><br>
&bull; check for src_ip to narrow the attackers, notice the web server's IP is included which is a red flag. Web servers are supposed to be passive and just responds, not the one that initiates<br>
<img width="552" height="23" alt="image" src="https://github.com/user-attachments/assets/0c18163b-4145-4473-924d-079a2a5cb556" /><br>
&bull; add the server's IP as the src_ip in your query, to cut down on fields displayed<br>
<img width="518" height="260" alt="image" src="https://github.com/user-attachments/assets/8373666e-757a-4320-a92f-1a2b772632fc" /><br>
&bull; check the packets and relevant fields such as the uri and the src_headers (contains the initiator's request)<br><br>


<h1>1.5 Find the fully qualified domain name (FQDN) used by the staging server hosting the defacement file.</h1>
<h2>ANSWER: prankglassinebracket.jumpingcrab.com</h2>
<img width="524" height="68" alt="image" src="https://github.com/user-attachments/assets/d4c15369-15e9-49d3-88df-073e958f238f" /><br>
&bull; the answer can be found from the same events, using the previous query<br><br>


<h1>2.1 In Level 1, you found the staging server domain name (used to host the defacement file). Find that server's IP address.</h1>
<h2>ANSWER: 23.22.63.114</h2>
<img width="1901" height="927" alt="image" src="https://github.com/user-attachments/assets/83abc6f7-3462-410d-a056-56fc22b3b73e" /><br>
&bull; the staging server's IP address can be mapped directly to the domain name found with the same query and events used in 1.4 & 1.5 on the dest_ip field<br><br>


<h1>2.3 Find the IP address performing a brute force attack against "imreallynotbatman.com".</h1>
<h2>ANSWER: 23.22.63.114</h2>
<img width="444" height="23" alt="image" src="https://github.com/user-attachments/assets/d4521223-df72-4c95-9340-28fbf1507dfd" /><br>
&bull; adjust the query to include the dest_ip which is the target of the brute force attack<br>
<img width="598" height="481" alt="image" src="https://github.com/user-attachments/assets/4f537eec-e611-4f0f-85fa-b4bc5baf3aaa" /><br>
&bull; check the uri/url and go to the login/admin section of the site
<img width="828" height="25" alt="image" src="https://github.com/user-attachments/assets/14b7ef98-bf13-4efe-b172-02c4ccc80f63" /><br>
&bull; since its login page, filter for POST method<br>
<img width="1919" height="946" alt="image" src="https://github.com/user-attachments/assets/bf26d568-dead-4c14-a6cd-dd7f101289be" /><br>
&bull; table can be an effective way to show the data cleanly and assess the time for potential signs of bruteforce attempts<br>
&bull; some cases user agents can show automated scripts or programs if not spoofed properly which can be an indicator of attack<br>
<img width="1904" height="945" alt="image" src="https://github.com/user-attachments/assets/c5f56fba-e9f3-4ebe-8e39-b3d4d3fc5468" /><br>
&bull; with http credentials are often in plaintext and are unsecured, in this case the field form_data holds the information of the attempts<br>
&bull; based on the info found in form_data and http_user_agents the IP 23.22.63.114 appears to be the malicious attacker conducting the bruteforce attempts<br><br>


<h1>2.4 Find the name of the executable file the attacker uploaded to the server</h1>
<h2>ANSWER: 3791.exe</h2>
<img width="622" height="36" alt="image" src="https://github.com/user-attachments/assets/edef8887-6ddc-47c6-a644-4350a6404a52" /><br>
&bull; check the packets or fields for relevant info<br>
<img width="585" height="256" alt="image" src="https://github.com/user-attachments/assets/25366177-1092-4429-9198-5d145e4b2e97" /><br>
&bull; in http part_filename field extracts names of files sent over the network, this can be relevant for future queries when you want to check if there are any uploaded files via http


<h1>3.1 In Level 2, you found the name of an executable file the attackers uploaded to the server. Find that file's MD5 hash.</h1>
<h2>ANSWER: 59A1D4FACD7B333F76C4142CD42D3ABA</h2><br>
<img width="650" height="20" alt="image" src="https://github.com/user-attachments/assets/f49f35fe-c5e4-4d68-8e37-421ba10d18d4" /><br>
&bull; file hashes and other relevant information related to the file can be checked in the XMLWinEventLog, so filter the source type in the relevant field.<br>
<img width="1622" height="202" alt="image" src="https://github.com/user-attachments/assets/1c8055c8-c046-4ec7-ad4e-3cb742636b2b" /><br>
&bull; after filtering the query check sample events for the hash info<br><br>


<h1>3.2What was the first brute force password used?</h1>
<h2>ANSWER: 12345678</h2>
<img width="1898" height="828" alt="image" src="https://github.com/user-attachments/assets/974925c8-551c-497d-b501-ae2b2a28f258" /><br>
&bull; filter the query and format data into table similar to 2.3, then filter the time to start from the earliest<br><br>


<h1>3.3 What was the correct password found in the brute force attack?</h1>
<h2>ANSWER: batman</h2>
<img width="1903" height="814" alt="image" src="https://github.com/user-attachments/assets/081a228a-bbac-4c08-8299-f9c204149819" /><br>
&bull; add an additional filter username=* to follow the common form_data format that was done on the login bruteforce attempts, then check near the end of the login attempts identifying a different src_ip and http user agent <br><br>

                                          
<h1>3.4 How many seconds elapsed between the time the brute force password scan identified the correct password and the compromised login? Round to 2 decimal places.</h1>
<h2>ANSWER: 92.17 secs</h2>
<img width="1905" height="352" alt="image" src="https://github.com/user-attachments/assets/0c7d71bb-1542-4736-9635-64729a817569" /><br>
&bull; since we know the password is batman we can add that to the query to filter out the instances where the password batman was used<br><br> 


<h1>3.5 How many unique passwords were attempted in the brute force attack?</h1>
<h2>ANSWER: 412</h2>
<img width="767" height="94" alt="image" src="https://github.com/user-attachments/assets/20b1ce06-e385-435b-9d97-9cd66145ba49" /><br>
&bull; to only capture the passwd field we can create a new field or use regex, in this case we will use regex since creating new fields isnt allowed in the splunk shared account. <br>
&bull; the syntax follows the | rex field=source_field "(extracted_fieldname[pattern])"<br>
&bull; the source field in this case is the form data where we will extract the passwd, the extracted field name can be the passwd or password, and the pattern is simply how the syntax would be extracted<br><br>
&bull; Regex Syntax Breakdown (`passwd=(?<password>[^&]+)`)<br>
&bull; **`passwd=`**  Literal match outside the group that anchors where the extraction begins. <br>
&bull; **`(?<password>...)`**  Named capture group. The `?` signals an extended regex group, and `<password>` assigns the captured text to a new field named `password`. <br>
&bull; **`[^&]`**  Negated character class (`^` inside brackets means NOT). Matches any character except an ampersand (`&`). <br>
&bull; **`+`**  Quantifier meaning "1 or more times". Consumes all non-`&` characters in sequence until hitting the delimiter. <br><br>


<h1>4.1 What was the most likely IP address of we8105desk on 24AUG2016?</h1>
<h2>ANSWER: 192.168.250.100</h2>
&bull; include the hostname in the query, then you can check either **'src'** or **`dest`** and map the ip from there, additionally group (aggregate) the data into clean tables to see the corresponding ip <br>
<img width="1910" height="661" alt="image" src="https://github.com/user-attachments/assets/442a478e-926b-4a63-a7c6-3e8a0a51a541" /><br>
OR
<img width="1916" height="375" alt="image" src="https://github.com/user-attachments/assets/5d25a5dd-c4f2-4938-8975-5dec956b0ef1" /><br>
&bull; both results show a consistent IP which is 192.168.250.100, and the other IP (127...) is a loopback<br><br>




