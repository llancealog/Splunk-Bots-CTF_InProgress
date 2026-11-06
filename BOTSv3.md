## 200: List out the IAM users that accessed an AWS service (successfully or unsuccessfully) in Frothly's AWS environment? Answer guidance: Comma separated without spaces, in alphabetical order. (Example: ajackson,mjones,tmiller)

### Answer: bstoll,btun,splunk_access,web_admin

- When investigating AWS activity in a SIEM tool, `aws:cloudtrail` is a go-to log source for initial context, it logs important information about the event such as the who, what, where, and when

<img width="321" height="38" alt="image" src="https://github.com/user-attachments/assets/224b89f6-e516-434a-908f-691e8da590ac" />

- Within the  `aws:cloudtrail` logs the `userIdentity.type` field is used to check whether the action was made by a user account, temporary role, root user (suspicious and should trigger an alert, rarely used for everyday operations), code, or an aws service.

<img width="597" height="296" alt="image" src="https://github.com/user-attachments/assets/590f2a8f-81e5-439a-8c1f-0b5c2de33d8b" />

- we can then check the field `userIdentity.userName` to show us the IAMUsers that accessed the AWS service

<img width="1265" height="375" alt="image" src="https://github.com/user-attachments/assets/d905a760-d24c-4ba4-b4d5-bd58f0852d78" />

---

## 201: What field would you use to alert that AWS API activity have occurred without MFA (multi-factor authentication)?

### Answer: userIdentity.sessionContext.attributes.mfaAuthenticated

- `userIdentity.sessionContext.attributes.mfaAuthenticated` this field is important to gain context on the login, but remember to also check `eventType` field to identify what type of event was logged
- normally if the `eventType` is console login not passing a MFA is a high risk and would most likely be a policy issue to be updated immediately. if the action was done by code or was logged as an AWS API Call in the event type it depends on the organization's policy, by default AWS API calls do not require MFA but it will be required if the organization has an explicit IAM policy that enforces MFA even for API calls. lastly are automated AWS services which is normal to appear as false or even missing entirely for MFA logs.

---
  
## 202: What is the processor number used on the web servers?

### Answer: E5-2676

- check the sourcetype `hardware` where it shows 3 different logs with the exact same specs of computers

<img width="777" height="744" alt="image" src="https://github.com/user-attachments/assets/10db37b4-81e8-4975-9f94-472f0253de18" />

- we can see the processor being  Intel(R) Xeon(R) CPU E5-2676 v3 @ 2.40GHz

---

## 203: Bud accidentally makes an S3 bucket publicly accessible. What is the event ID of the API call that enabled public access?

### Answer: ab45689d-69cd-41e7-8705-5350402cf7ac

- to get an idea on who Bud is we can do a quick wide scope query as such

<img width="158" height="31" alt="image" src="https://github.com/user-attachments/assets/479d56b1-2fbf-4dc1-8e0b-99bdf7b1d763" />

- and from there we can identify Bud as Bud Stoll or an IAM user we encountered as `bstoll`
- from here we can go back to `aws:cloudtrail` to gather information from there with a query as such

<img width="433" height="31" alt="image" src="https://github.com/user-attachments/assets/052083e3-cdfa-49e6-8ae6-1906b7ec23bd" />

- now we want to check the events like `eventName` for the description of the event that happened

<img width="1825" height="514" alt="image" src="https://github.com/user-attachments/assets/09d33034-5af5-4cb2-8617-334ef2d758b7" />

- based on the descriptions the most likely culprit would be the `PutBucketAcl` which is the action of putting / writing new permissions into the access control list
    - `PUT` = write / upload / create
    - `GET` = read / download / view
    - `LIST` = directory view (wide view)
    - `DELETE` = remove

- now by updating the query to include the `PutBucketAcl` field we can check the other info on the logs most notibly the `requestParameters.AccessControlPolict` fields

<img width="1054" height="803" alt="image" src="https://github.com/user-attachments/assets/62aab85b-9f9e-457c-9eab-b9a8127eaed0" />

- from these fields specifically the `requestParameters.AccessControlPolicy.AccessControlList.Grant{}.Grantee.URI` field we can see the uri path of the groups that are being given privilege because of that action including paths like `/global/AllUsers`
- this confirms the specific event log we are looking for all thats left is to check the corresponding event ID

<img width="867" height="25" alt="image" src="https://github.com/user-attachments/assets/90ddb1c3-d010-475a-9397-51ef89179519" />

---

## 204: What is the name of the S3 bucket that was made publicly accessible?

### Answer: frothlywebcode

- it can be found in the same log as the previous question with the field `requestParameters.bucketName`

<img width="723" height="24" alt="image" src="https://github.com/user-attachments/assets/aa257349-ff01-4cea-bcc1-80c208823e8d" />

---

## 205: What is the name of the text file that was successfully uploaded into the S3 bucket while it was publicly accessible?

### Answer: OPEN_BUCKET_PLEASE_FIX.txt

- I originally tried checking `aws:cloudtrail` for logs that involve .txt or .docx but it seems that cloudtrail does not log anonymous uploads though it can log anonymous management events like creating, editing, or deleting buckets
- with this in mind we proceed with a different sourcetype, since we're trying to find a file uploaded on the S3 bucket we checl `aws:s3:accesslogs`

<img width="608" height="133" alt="image" src="https://github.com/user-attachments/assets/57456631-6dd6-4a4e-bc7a-20a18a79a66c" />

- with this query we found 3 logs all with the exact same txt filename, and the earliest log was uploaded just a minute after the bucket was set to public (9:01:46.000)

<img width="1532" height="74" alt="image" src="https://github.com/user-attachments/assets/87a5fe8c-5ed0-45af-849e-e973475134c3" />

---

## 206: What is the size (in megabytes) of the .tar.gz file that was successfully uploaded into the S3 bucket while it was publicly accessible? (Answer guidance: Round to two decimal places without the unit of measure. Use 1024 for the byte conversion. Use a period as the radix character.)

### Answer: 2.93 Mb

<img width="510" height="32" alt="image" src="https://github.com/user-attachments/assets/a9333698-a6b8-4802-b78e-6e7062519f25" />

- we can use this query to find logs that involve `tar.gz`
- but to further narrow down the results we'd want to set the http_method to PUT since the question says uploaded

<img width="664" height="110" alt="image" src="https://github.com/user-attachments/assets/498ff2dc-83a5-4d7e-b612-690dcff2103e" />

<img width="1533" height="117" alt="image" src="https://github.com/user-attachments/assets/741f0e3f-8f03-473e-b171-d436210bbe09" />

- we'd want to check the log that happened around the same time that the S3 bucket was made public

<img width="266" height="27" alt="image" src="https://github.com/user-attachments/assets/df03214b-de10-4374-a6fa-71b21f994ca1" />

- its object size shows 3076532 in bytes, according to the instruction we convert it using the 1024 method, we'd want to do it twice to get the mb
- 3076532 bytes --> 3004.43 kilobytes --> 2.93 megabytes

---

## 208: A Frothly endpoint exhibits signs of coin mining activity. What is the name of the first process to reach 100 percent CPU processor utilization time from this activity on this endpoint? (Answer guidance: Include any special characters/punctuation.)

### Answer: chrome#5

- we need to check the sourcetype `perfmonmk:process` because it tracks the resource utilization of individual applications into events

<img width="326" height="47" alt="image" src="https://github.com/user-attachments/assets/ee35831c-4d19-479a-b207-e2b8061239ce" />

- then we check for other relevant fields, where we can find `process_cpu_used_percent` so we add that to the query so Splunk only shows us processes that reached 100 percent cpu usage

<img width="535" height="46" alt="image" src="https://github.com/user-attachments/assets/5ed05a8d-f162-4df3-8902-c5e600cdd7f7" />

<img width="1535" height="804" alt="image" src="https://github.com/user-attachments/assets/95f06629-1ac6-478f-8ed1-59e108d6278a" />

- from these logs we can say that `MicrosoftEdgeCP#2` was the first process to cap the CPU at 100 percent but it seems that it was due to a different reason and not crypto mining
- based on the logs we can see that there is a much more suspicious activity where a sequence of chrome processes can be seen all reaching 100% CPU usage
- leading us to the answer `chrome#5`

---

## 209: When a Frothly web server EC2 instance is launched via auto scaling, it performs automated configuration tasks after the instance starts. How many packages and dependent packages are installed by the cloud initialization script? Provide the number of installed packages then number of dependent packages, comma separated without spaces.

### Answer: 7, 13

- use `cloud-init-output` as the sourcetype, this is basically the logs of EC2 instance during its first initialization
- this one is a bit difficult to find due to the lack of TA fields, but searching the keyword "`dependent packages`" can easily cut down the noise. using "packages" as the keyword also works but youd have to look and scroll through terminal logs of each event

<img width="1200" height="500" alt="image" src="https://github.com/user-attachments/assets/09fe2d4f-ab19-4a9a-bccf-6483708e4f59" />

---

## 210: What is the short hostname of the only Frothly endpoint to actually mine Monero cryptocurrency? (Example: ahamilton instead of ahamilton.mycompany.com)   

### Answer: BSTOLL-L

- for this we can use question 208 as a basis where all of the events where generated by the host `BSTOLL-L`

<img width="889" height="516" alt="image" src="https://github.com/user-attachments/assets/59cc55c5-44c0-44f0-ae9e-e035861bebee" />

- a different approach could also be done by searching the keyword `coinhive` which was the most famous and dominant browser-based cryptominer at the time before it got closed down in 2019

<img width="883" height="508" alt="image" src="https://github.com/user-attachments/assets/4029ad54-fa11-4452-821c-7203315766f6" />

- using this approach we could actually see 2 different hosts that are connected to coinhive, to further verify we should check for other evidences / indicators of cryptomining which often pushes CPU usage upto 100%
- it turns out this dataset only has BSTOLL's `perfmonk` logs and not the others, so we need to verify in a different way in this case we can check the `event_type`
- for cryptomining to occur it would need to establish a connection between the infected device and a device that would send mining jobs that are needed to be solved.

<img width="875" height="853" alt="image" src="https://github.com/user-attachments/assets/6be01f76-5419-44ea-938b-6f44137d49ac" />

- by checking the `message_type` QUERY we could see that all those logs only had one host which was BSTOLL-L, which is most likely the host to have the cryptomining activity

  <img width="880" height="498" alt="image" src="https://github.com/user-attachments/assets/627eb7a3-e13d-4101-9490-5c134a03bfe1" />

---

## 211: How many cryptocurrency mining destinations are visited by Frothly endpoints?

### Answer: 6

- to answer this question we can continue from question `210` where we check the field `message_type` with the value of QUERY to see the logs that recorded when a connection is established
- then we check the `name` field to identify the different destinations that a connection has been established with

<img width="874" height="897" alt="image" src="https://github.com/user-attachments/assets/f8f2a1c2-89d1-4446-ad44-8dc20be4127c" />

---

## 212: Using Splunk's event order functions, what is the first seen signature ID of the coin miner threat according to Frothly's Symantec Endpoint Protection (SEP) data?

### Answer:
