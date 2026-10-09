<img width="1205" height="936" alt="image" src="https://github.com/user-attachments/assets/b27fac86-64f7-4a6a-8a3b-e03a0520865f" /><img width="1903" height="771" alt="image" src="https://github.com/user-attachments/assets/813c9318-74fe-4306-9462-c2fa63106545" /># Splunk-Bots-CTF V1

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
&bull; include the hostname in the query, then you can check either <b><code>src</code></b> or <b><code>dest</code></b> and map the ip from there, additionally group (aggregate) the data into clean tables to see the corresponding ip <br>
<img width="1910" height="661" alt="image" src="https://github.com/user-attachments/assets/442a478e-926b-4a63-a7c6-3e8a0a51a541" /><br>
OR
<img width="1916" height="375" alt="image" src="https://github.com/user-attachments/assets/5d25a5dd-c4f2-4938-8975-5dec956b0ef1" /><br>
&bull; both results show a consistent IP which is 192.168.250.100, and the other IP (127...) is a loopback<br><br>


<h1>4.2Amongst the Suricata signatures that detected the Cerber malware, which one alerted the fewest number of times? Submit ONLY the signature ID value as the answer. (No punctuation, just 7 integers.)</h1>
<h2>ANSWER: 2816763</h2>
<img width="393" height="45" alt="image" src="https://github.com/user-attachments/assets/0365be9e-d242-4e18-a70e-69daaa6422ef" /><br>
&bull; since we are looking for an alert set the event type to alert<br>
<img width="576" height="41" alt="image" src="https://github.com/user-attachments/assets/be9743f3-f7f9-4050-8ba0-cd0417210825" /><br>
&bull; check the interesting fields section for the <b><code>alert.signature</code></b> and add the query to filter only showing cerber<br>
<img width="599" height="329" alt="image" src="https://github.com/user-attachments/assets/57af6a23-b968-4c98-9bfe-d90219bbae5e" /><br>
&bull; check the fields for the alert that popped the least, then map it to the corresponding signatureid<br>
<img width="881" height="365" alt="image" src="https://github.com/user-attachments/assets/da784362-1024-4504-9268-1984c466e966" /><br><br>


<h1>4.3 What fully qualified domain name (FQDN) does the Cerber ransomware attempt to direct the user to at the end of its encryption phase?</h1>
<h2>ANSWER: cerberhhyed5frqa.xmfir0.win</h2>
<img width="1067" height="32" alt="image" src="https://github.com/user-attachments/assets/3913a14b-f51d-4f35-845d-9c922ba8c045" /><br>
&bull; from the prior query change the <b><code>alert.signature</code></b> to the one with onion domain lookup since we are looking for domain<br>
&bull; take note of the time the alert was generated and use that in the next step for finding the query involving its dns<br>
<img width="523" height="39" alt="image" src="https://github.com/user-attachments/assets/a4560a7a-4768-422f-9bb3-848095748fcc" /><br>
&bull; change the <b><code>event_type</code></b> from alert to dns, and change the time to be near the time that the alert popped<br>
<img width="595" height="305" alt="image" src="https://github.com/user-attachments/assets/8ed16837-185b-4bbd-b599-5d1d41406828" /><br>
&bull; now check the <b><code>dns.rrname</code></b> (resource record name - basically the domain or host name that is being asked for) for the suspicious domain<br><br>


<h1>4.4 What was the first suspicious domain visited by we8105desk on 24AUG2016?</h1>
<h2>ANSWER: solidaritedeproximite.org</h2>
<img width="452" height="29" alt="image" src="https://github.com/user-attachments/assets/8fd88870-765c-4f9b-ad4a-0ddd9028a206" /><br>
&bull; to check the suspicious domain visited by we8105desk we look into <b><code>stream:http</code></b> as the source<br>
<img width="1649" height="580" alt="image" src="https://github.com/user-attachments/assets/a2cafdb2-c4e3-4a09-b02b-76294301c91d" /><br>
&bull; then we can aggregate the results into a clean table and adjust the date accordingly to August 24 2016<br><br>
<img width="1848" height="585" alt="image" src="https://github.com/user-attachments/assets/71e669bb-2cff-431c-8542-9668e3dc4abb" /><br><br>


<h1>4.5 During the initial Cerber infection a VB script is run. The entire script from this execution, pre-pended by the name of the launching .exe, can be found in a field in Splunk. What is name of the first function defined in the VB script?</h1>
<h2>ANSWER: GNbiPpPt5SZ1</h2>
<img width="1892" height="934" alt="image" src="https://github.com/user-attachments/assets/52f06e96-174a-41b6-8c63-80a41b08e4b6" /><br>
&bull; then by checking the logs a large block of obsfucated code can be seen in the commandline which is a red flag that should be investigated <br>
<img width="599" height="986" alt="image" src="https://github.com/user-attachments/assets/493e40d8-ee9e-4d99-a309-8a81597939d8" /><br>
&bull; in the <b><code>CommandLinee</code></b> field you can see the log with obfuscated code and from you can see the words FuNCtion GNbiPpPt5SZ1


<h1>4. 6During the initial Cerber infection a VB script is run. The entire script from this execution, pre-pended by the name of the launching .exe, can be found in a field in Splunk. What is the length in characters of the value of this field?</h1>
<h2>ANSWER: 4490</h2>
&bull; still connected to 4.5, we are asked to get the length of the characters in the commandline where the VBS script was ran<br>
<img width="1891" height="963" alt="image" src="https://github.com/user-attachments/assets/309291c9-86d6-4324-aaa8-d6edeed67dfe" /><br>
&bull; just use the <b><code>eval</code></b> function along with <b><code>len()</code></b> and the field to get its length<br><br><br>


<h1>4.7 What is the name of the USB key inserted by Bob Smith?</h1>
<h2>ANSWER: MIRANDA_PRI</h2>
<img width="627" height="39" alt="image" src="https://github.com/user-attachments/assets/14820233-1709-4d40-9336-69a672896dc9" /><br>
&bull; I started with this query to assess the situation, since the standard EventCodes or event ids that shows a device was plugged returned no results I used the USB keyword
<img width="1604" height="56" alt="image" src="https://github.com/user-attachments/assets/8c169c3f-34e7-4704-8e2e-f91b56b1c9b1" /><br>
&bull; from the message we found out that there is a USB and the message or logs generated was about the USB not being able to be ejected, but what we found was the combination of vendor id (VID), product id (PID) and the device's unique serial number, not the human readable name.<br>
<img width="385" height="34" alt="image" src="https://github.com/user-attachments/assets/240d680c-f423-4426-b275-90257688e2c4" /><br>
&bull; add the keyword friendlyname into your query this is similar to device desc in your registry editor, only difference is that friendly name is specifically for storage drives and user-facing peripherals<br>
<img width="310" height="56" alt="image" src="https://github.com/user-attachments/assets/87db60d0-eede-43a4-bb6e-0fd0b87842e0" /><br>



<h1>4.8 Bob Smith's workstation (we8105desk) was connected to a file server during the ransomware outbreak. What is the domain name of the file server?</h1>
<h2>ANSWER: 	we9041srv.waynecorpinc.local</h2>
<img width="447" height="31" alt="image" src="https://github.com/user-attachments/assets/923570fb-b715-4f19-8b58-498c32e1d41e" /><br>
&bull; use SMB as source when investigating file servers, with the query add we8105desk's IP as the source<br>
<img width="588" height="289" alt="image" src="https://github.com/user-attachments/assets/e94e8c1b-6a38-4425-aef7-818547d168ea" /><br>
&bull; check <b><code>dest_ip</code></b> field, based on volume alone .20 is the most likely culprit, .255 is broadcast which is ignorable, and upon checking .2.50 I found that it didnt have a proper domain / hostname, only something like waynecorpinc.local<br>
<img width="785" height="32" alt="image" src="https://github.com/user-attachments/assets/5829cfed-d668-4512-92f6-42f13174a929" /><br>
&bull; proceed with this query adding the <b><code>path</code></b> field to your query (path in smb logs basically shows the hostname/IP along with the directory or file path being accessed)<br>
<img width="1912" height="677" alt="image" src="https://github.com/user-attachments/assets/088b3579-e110-434c-bffb-09ea9b1f8a43" /><br>
&bull; cleanly present the data into a tabled format highlighting the relevant field for quick analysis, showing that all fields point to we9041srv<br><br>


<h1>4.9 Bob Smith's workstation (we8105desk) was connected to a file server during the ransomware outbreak. What is the IP address of the file server?</h1>
<h2>ANSWER: 	192.168.250.20</h2>
<img width="531" height="90" alt="image" src="https://github.com/user-attachments/assets/f6bf402a-2f95-4b12-a986-3719e81a4fd2" /><br>
&bull; let's say that we didnt decide to specifically check the .20 field, use this query to show you all logs that contains value in the path, then format it cleanly into structured table for analysis<br>
<img width="1919" height="421" alt="image" src="https://github.com/user-attachments/assets/7fcaa649-5936-4e78-92d3-b793ca8b4c76" /><br>
&bull; the table allows us to easily map the we9041srv hostname of the server to its corresponding IP address<br><br>


<h1>4.10 How many distinct PDFs did the ransomware encrypt on the remote file server?</h1>
<h2>ANSWER: 	257</h2>
<img width="486" height="142" alt="image" src="https://github.com/user-attachments/assets/acf5a584-fa7b-468f-9de9-468ee569f1f4" /><br>
&bull; do a broad query with the information gathered from the question (pdfs & the remote file server)<br>
<img width="757" height="435" alt="image" src="https://github.com/user-attachments/assets/2ca5bbc6-330e-46eb-8572-64994e514b92" /><br>
&bull; from the results we could see the <b><code>Relative_Target_Name</code></b> field contains the pdf names so add that to our query<br>
<img width="877" height="686" alt="image" src="https://github.com/user-attachments/assets/6bd28450-9441-4c6b-9f64-bb33e206640a" /><br>
&bull; check the <b><code>Accesses</code></b> field for relevant info in this case DELETE is actually important, because in SMB logs it is the final step in the renaming process, where the system is the one that creates the log deleting the old name and replacing it with the new<br>
&bull; i forgot to mention that attackers / ransomware often renames the file extensions (e.g. .cerber)<br>
<img width="1889" height="874" alt="image" src="https://github.com/user-attachments/assets/975014e9-a2f6-4755-967e-b154ef6a5aac" /><br><br>


<h1>4.11 The VBscript found above launches 121214.tmp. What is the ParentProcessId of this initial launch?</h1>
<h2>ANSWER:3968</h2>
<img width="763" height="34" alt="image" src="https://github.com/user-attachments/assets/ac9b3cf0-9876-49b1-b171-86972117ca19" /><br>
&bull; use this general query adding related keywords <br>
<img width="592" height="296" alt="image" src="https://github.com/user-attachments/assets/4dfc3af4-9ee0-481f-b7af-87fc49de8786" /><br><br>


<h1>4.12 The Cerber ransomware encrypts files located in Bob Smith's Windows profile. How many .txt files does it encrypt?</h1>
<h2>ANSWER: 406</h2>
<img width="726" height="28" alt="image" src="https://github.com/user-attachments/assets/5bf6c18b-b4e9-462e-b795-f37495db103f" /><br>
&bull; do a general search for txt files in bob's machine, check relevant fields for information like <b><code>EventDescription</code></b>, <b><code>TargetFilename</code></b>, <b><code>file_path</code></b><br>
<img width="597" height="264" alt="image" src="https://github.com/user-attachments/assets/c41a66af-04c8-4e18-9fad-2eca304f3311" /><br>
&bull; notice File Create Time having majority of the logs, upon researching what it is and viewing from a malicious context, this is highly suspicious as threat actors may change the meta data of files like time, so that it doesnt appear as recently edited. This is an sub-technique called Timestomp that can be mapped with the MIRE ATT&CK framework under the stealth tactics.<br>
<img width="593" height="605" alt="image" src="https://github.com/user-attachments/assets/dceb03a8-eac0-4a75-9fb3-c5d48cbd3f7f" /><br>
&bull; file path and target file name has the same contents, showing both the path and the target file. Based on the question, it asks for the encrypted files on bob's windows profile, and in the file path we can see the different text files along with bob's file path, but it also includes others.<br>
<img width="1107" height="153" alt="image" src="https://github.com/user-attachments/assets/d4f8777b-3c88-47b7-89c0-035189b80441" /><br>
&bull; in this case we modify the query so it only shows us txt files inside bob's windows profile<br><br>


<h1>4.13 The malware downloads a file that contains the Cerber ransomware cryptor code. What is the name of that file?</h1>
<h2>ANSWER: mhtr.jpg</h2>
<img width="1904" height="660" alt="image" src="https://github.com/user-attachments/assets/391d0e27-1e34-40c0-9ef8-a3fa78929e48" /><br>
&bull; as we can see from the image the .vbs was created by the malicious code block, and this vb script after a few minutes executed the tmp file at the bottom which is the ransomware<br>
<img width="643" height="159" alt="image" src="https://github.com/user-attachments/assets/c76f8074-ebf2-4d91-8ed0-24dbe1e6a82b" /><br>
&bull; for our next query to find the cryptor code (part of the ransomware responsible for locking files) we set the date to all logs prior to the execution of the ransomware.<br>
<img width="579" height="79" alt="image" src="https://github.com/user-attachments/assets/e3fc1bf2-5a73-4d66-825e-7ce828466678" /><br>
&bull; we know check the network traffic to see what bob's workstation downloaded before the ransomware executed. we then filter into clean tabled format sorted by time
<img width="1897" height="414" alt="image" src="https://github.com/user-attachments/assets/928588e5-4156-427b-8248-c9e41e5ca50b" /><br>
&bull; showing us the last log downloaded (GET) by bob's workstation before the ransomware executes, which is also a suspicious domain that was noted in a prior question<br><br>


<h1>4.14 Now that you know the name of the ransomware's encryptor file, what obfuscation technique does it likely use?</h1>
<h2>ANSWER: </h2>
&bull; based on the logs we found in Splunk, it shows the extension as jpg. so most likely we could classify this as masquerading or extension spoofing, where a file disguises itself as a different type of file by changing its extension<br><br>



