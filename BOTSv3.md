## 200: List out the IAM users that accessed an AWS service (successfully or unsuccessfully) in Frothly's AWS environment? Answer guidance: Comma separated without spaces, in alphabetical order. (Example: ajackson,mjones,tmiller)

### Answer: bstoll,btun,splunk_access,web_admin

When investigating AWS activity in a SIEM tool, `aws:cloudtrail` is a go-to log source for intial context, it logs important information about the event such as the who, what, where, and when

