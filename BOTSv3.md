## 200: List out the IAM users that accessed an AWS service (successfully or unsuccessfully) in Frothly's AWS environment? Answer guidance: Comma separated without spaces, in alphabetical order. (Example: ajackson,mjones,tmiller)

### Answer: bstoll,btun,splunk_access,web_admin

- When investigating AWS activity in a SIEM tool, `aws:cloudtrail` is a go-to log source for initial context, it logs important information about the event such as the who, what, where, and when

<img width="321" height="38" alt="image" src="https://github.com/user-attachments/assets/224b89f6-e516-434a-908f-691e8da590ac" />

- Within the  `aws:cloudtrail` logs the `userIdentity.type` field is used to check whether the action was made by a user account, temporary role, root user (suspicious and should trigger an alert, rarely used for everyday operations), code, or an aws service.

<img width="597" height="296" alt="image" src="https://github.com/user-attachments/assets/590f2a8f-81e5-439a-8c1f-0b5c2de33d8b" />

- we can then check the field `userIdentity.userName` to show us the IAMUsers that accessed the AWS service

<img width="1265" height="375" alt="image" src="https://github.com/user-attachments/assets/d905a760-d24c-4ba4-b4d5-bd58f0852d78" />


## 201: What field would you use to alert that AWS API activity have occurred without MFA (multi-factor authentication)?

### Answer: userIdentity.sessionContext.attributes.mfaAuthenticated

- `userIdentity.sessionContext.attributes.mfaAuthenticated` this field is important to gain context on the login, but remember to also check `eventType` field to identify what type of event was logged
- normally if the `eventType` is console login not passing a MFA is a high risk and would most likely be a policy issue to be updated immediately. if the action was done by code or was logged as an AWS API Call in the event type it depends on the organization's policy, by default AWS API calls do not require MFA but it will be required if the organization has an explicit IAM policy that enforces MFA even for API calls. lastly are automated AWS services which is normal to appear as false or even missing entirely for MFA logs.

  
## 202: What is the processor number used on the web servers?

### Answer: E5-2676

- check the sourcetype `hardware` where it shows 3 different logs with the exact same specs of computers

<img width="777" height="744" alt="image" src="https://github.com/user-attachments/assets/10db37b4-81e8-4975-9f94-472f0253de18" />

- we can see the processor being  Intel(R) Xeon(R) CPU E5-2676 v3 @ 2.40GHz


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














