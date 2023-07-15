![[Screenshot 2023-06-04 at 11.56.49 AM.png]]

Here:
- Alice, Bob and Charles have the policies which are assigned to the `Developers` group.
- Charles and David have policies which are assigned to the `Audit team` group.
	- Charles will have policies from both `Developers` and `Audit team` group.
	- David will have policies from both `Audit team` group and `Operations` group.
- David and Edward have policies which are assigned to the `Operations` group.
- Fred does not have any group, so he'll only have policies which are assigned directly to him.

- IAM policies structure, contains
	- version
	- Id
	- Statement/s , `[]`
		- `SID`: statement ID
		- `Effect`: `Allow` / `Deny`
		- `Principal`: account/user/role to which this policy applied to
		- `Action`: list of actions this policy allows to deny
		- `Resources`: list of resources to which this actions applied to
		- `Condition`: (Optional) when this policy is in effect
```json
{
	"Version": "2012-10-17",
	"Id": "S3-Account-Permissions",
	"Statement": [
		{
			"Sid": "1",
			"Effect": "Allow",
			"Principal": {
				"AWS": ["arn:aws:iam::12312312312:root"]
			},
			"Action": [
				"s3:GetObject",
				"s3:PutObject"
			],
			"Resource": ["arn:aws:s3:::mybucket/*"]
		}
	]
}
```
