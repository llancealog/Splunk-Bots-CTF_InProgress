## 200: List out the IAM users that accessed an AWS service (successfully or unsuccessfully) in Frothly's AWS environment? Answer guidance: Comma separated without spaces, in alphabetical order. (Example: ajackson,mjones,tmiller)

### Answer: bstoll,btun,splunk_access,web_admin

- When investigating AWS activity in a SIEM tool, `aws:cloudtrail` is a go-to log source for initial context, it logs important information about the event such as the who, what, where, and when

<img width="321" height="38" alt="image" src="https://github.com/user-attachments/assets/224b89f6-e516-434a-908f-691e8da590ac" />

- Within the  `aws:cloudtrail` logs the `userIdentity.type` field is used to check whether the action was made by a user account, temporary role, root user (suspicious and should trigger an alert, rarely used for everyday operations), code, or an aws service.
